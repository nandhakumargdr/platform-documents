
Step 1: Create a new dotnet core angular project
$ dotnet new angular -n Todo

Step 2:
Delete the ClientApp (Angular project)

Step 3:
Create new angular 10 project
$ cd Todo
$ ng new ClientApp --style=scss --routing

Step 4: 
Add the express engine of Angular Universal to the project
$ cd ClientApp
$ ng add @nguniversal/express-engine 

Step 4:
Install the aspnet-prerendering node package
npm install --save aspnet-prerendering

Step 5:
Update the dotnet core startup.cs - replies app.userSpa middleware with the below code

```cs
app.UseSpa(spa =>
	{
		spa.Options.SourcePath = "ClientApp";

		spa.UseSpaPrerendering(options =>
		{
			// This is the path where angular generates the server build result
			// This path would be different when using React or Vue
			options.BootModulePath = $"{spa.Options.SourcePath}/dist/server/main.js";
			
			// During development, let angular run the build:ssr command on each build.
			// Check the package.json to see what this command does.
			// When deploying your application on a production server, the ng build:ssr command will be executed to generate the server build.
			// Check the csproj file to see what commands are being run on publish.
			// The PublishRunWebpack target runs `npm install` `npm run build -- --prod` and `npm run build:ssr`
			options.BootModuleBuilder = env.IsDevelopment()
				? new AngularCliBuilder(npmScript: "build:ssr")
				: null;
			
			options.ExcludeUrls = new[] { "/sockjs-node" };
		});

		if (env.IsDevelopment())
		{
			spa.UseAngularCliServer(npmScript: "start");
		}
	});
}
```

Step 5:
Update tsconfig.server.ts
Add "module": "commonjs"

```ts
{
  "extends": "./tsconfig.app.json",
  "compilerOptions": {
    "outDir": "./out-tsc/server",
    "target": "es2016",
    "module": "commonjs",
    "types": [
      "node"
    ]
  },
  "files": [
    "src/main.server.ts",
    "server.ts"
  ],
  "angularCompilerOptions": {
    "entryModule": "./src/app/app.server.module#AppServerModule"
  }
}
```


Step 5:
Replace whole code from the main.server.ts with the below code
```ts
/***************************************************************************************************
 * Load `$localize` onto the global scope - used if i18n tags appear in Angular templates.
 */
// import '@angular/localize/init';
import 'zone.js/dist/zone-node';
import 'reflect-metadata';
import { renderModule, renderModuleFactory } from '@angular/platform-server';
import { APP_BASE_HREF } from '@angular/common';
import { enableProdMode, StaticProvider, Inject } from '@angular/core';
import { createServerRenderer, BootFuncParams } from 'aspnet-prerendering';
export { AppServerModule } from './app/app.server.module';

enableProdMode();

const getBaseUrl = (params: BootFuncParams) => {
  return params.origin + params.baseUrl.slice(0, -1);
};

export default createServerRenderer(params => {
  const { AppServerModule, AppServerModuleNgFactory, LAZY_MODULE_MAP } = (module as any).exports;

  const providers: StaticProvider[] = [
    { provide: APP_BASE_HREF, useValue: params.baseUrl },
    { provide: 'BOOT_PARAMS', useValue: params },
    { provide: 'BASE_URL', useFactory: getBaseUrl, deps: ['BOOT_PARAMS'] },
    { provide: 'SERVERSIDE', useValue: true },
  ];

  const options = {
    document: params.data.originalHtml,
    url: params.url,
    extraProviders: providers
  };

  // Bypass ssr api call cert warnings in development
  process.env.NODE_TLS_REJECT_UNAUTHORIZED = '0';

  const renderPromise = AppServerModuleNgFactory
    ? /* AoT */ renderModuleFactory(AppServerModuleNgFactory, options)
    : /* dev */ renderModule(AppServerModule, options);

  return renderPromise.then(html => ({ html }));
});

export { renderModule, renderModuleFactory } from '@angular/platform-server';
```


Step 6:
Update angular.json

projects.ClientApp.architect.build.options ->
```json
{
  "outputPath": "dist"
}
```

projects.ClientApp.architect.server.options -> 
```json
{
  "outputPath": "dist/server",
  "main": "src/main.server.ts"
}
```

Step 7:
Edit .csproj file
Comment the below line to avoid creating node_modules folder on build folder
<DistFiles Include="$(SpaRoot)node_modules\**" Condition="'$(BuildServerSideRenderer)' == 'true'" />
Find for -- and remove it

```
$ npm run build -- --prod to npm run build --prod
$ npm run build:ssr — --prod to npm run build:ssr --prod
```
Step 8:
Disable HttpsRedirection middleware from startup.cs


========================================

For NodeJs 
https://www.djamware.com/post/5f1f7b3360eaaf117a3022d5/angular-10-universal-server-side-rendering-ssr-crud-example
