Testing funcional con gulp, jasmine, webdriveio y selenium
==========================================================

El objetivo de este tutorial es mostrar la ejecucion de pruebas funcionales gulp, jasmine, webdriveio y selenium

##Requisitos
Tener en cuenta que para windows no es necesario usar "sudo".

  1. Instalar la ultima versión de [nodejs](https://nodejs.org) ,si ya se encuentra instalada actualizarla usando el comando "sudo npm install npm -g" .
  2. Instalar la ultima version de [gulp](http://gulpjs.com) usando el comando "sudo npm install gulp -g". En caso ya se tenga instalado desinstalar ("sudo npm uninstall gulp -g") y volver a instalar.
  3. Instalar la ultima version de [jasmine](http://jasmine.github.io) usando el comando "sudo npm install jasmine -g".
  4. Descargar [selenium server](http://www.seleniumhq.org) version 2.45.0 y copiar en la carpeta "./bin" (asegurarse que el nombre sea selenium-server-standalone-2.45.0.jar).
  5. Tener instalado java (jdk 6+).
  6. Instalar firefox ( :( no pude integrarlo correctamente con phantom.js).

##Nodejs
Plataforma construida sobre el runtime de Chrome para Javascript.
Para conocer un poco de node, crear el archivo demo.js con el siguiente contenido:
```js
var a = 1;
console.log('a: %s',a);
```
Ejecutar en la consola
```sh
node demo.js
```
Debe dar como resultado:
```js
a: 1
```
Se recomienda siempre inicializar el proyecto usando el siguiente comando:
```sh
sudo npm init
```
Luego de completar la inicialización se creará un archivo package.json, este archivo tendra permisos con el usuario root los cuales debemos cambiar al usuario local (revisar comando chown).
Para mayor detalle de lo que se puede hacer revisar la [web](https://nodejs.org/api).

##Gulp
Automatiza y mejora los flujos de trabajo. Usa un paradigma similar al de make.
Para iniciar con gulp ejecutar el siguiente comando:
```sh
npm install gulp --save-dev
```
Es importante tener en cuenta el argumento --save-dev debido a que esta dependencia solo será usada para el desarrollo.
Una vez termine de descargar gulp se deberá crear el archivo gulpfile.js con el siguiente contenido:
```js
var gulp = require('gulp');

gulp.task('default', function() {
  // codigo!!!
});
```
Para mayor detalle revisar la documentación del [api](https://github.com/gulpjs/gulp/blob/master/docs/API.md) y la lista completa de [plugins](http://gulpjs.com/plugins).

##Jasmine
[BDD](http://en.wikipedia.org/wiki/Behavior-driven_development) para javascript.
A fin de iniciar jasmine se deberá ejecutar el siguiente comando:
```sh
jasmine init
```
Se creará la carpeta "spec" que contiene los tests.
Creamos el archivo first.spec.js y agregamos el siguiente contenido:
```js
describe("Un test suite", function() {
  it("contenido de un spec", function() {
    expect(true).toBe(true);
  });
});
```
Para ver los resultados de nuestro test ejecutamos el siguiente comando:
```sh
jasmine
```

Para mayor detalle revisar la documentación del [api](http://jasmine.github.io/2.3/introduction.html)

#WebdriveIO y Selenium
Herramienta para usar selenium desde nodejs.
Para usar webdriverio debemos agregar la dependencia:
```sh
npm install webdriverio --save-dev
```
Debido a que webdriverio usa selenium debemos iniciar selenium, para eso ejecutar el siguiente comando en otra consola:
```sh
./selenium
```
Una vez iniciado selenium se procede a crear el archivo test.js con el siguiente contenido:
```js
var webdriverio = require('webdriverio');
var options = {
  desiredCapabilities: {
    browserName: 'firefox'
  }
};
webdriverio
  .remote(options)
  .init()
  .url('http://www.google.com')
  .title(function(err, res) {
    console.log('Title was: ' + res.value);
  })
  .end();
```
Para ejecutar nuestro test debemos ejecutar lo siguiente:
```js
node test.js
```
A fin de encontrar mayor detalle de webdriverio revisar la documentación [general](http://webdriver.io/guide.html) y del [api](http://webdriver.io/api.html)

#Todo junto!

Agregar las dependencias de gulp, gulp-jasmine y webdriverio
```
npm install gulp --save-dev
npm install gulp-jasmine --save-dev
npm install webdriverio --save-dev
```
Actualizar el archivo gulpfile.js con el siguiente contenido:
```
var gulp = require('gulp');
var jasmine = require('gulp-jasmine');

gulp.task('test', function() {
  
  gulp.src('spec/**.spec.js').pipe(jasmine());
});

gulp.task('default',['test'] ,function () {
  
});
```
En la carpeta spec crear el archivo github_jasmine.spec.js con el siguiente contenido:
```js
var webdriverio = require('webdriverio');

describe('En la web de github', function() {

  var client = {};
  jasmine.DEFAULT_TIMEOUT_INTERVAL = 9999999;

  beforeEach(function() {
    client = webdriverio.remote({ desiredCapabilities: {browserName: 'firefox'} });
    client.init();
  });

  it('Verificamos el titulo de la pagina', function(done) {
    client
      .url('https://github.com/')
      .getTitle(function(err, title) {
        expect(err).toBeFalsy();
        expect(title).toBe('GitHub · Build software better, together.');
      })
      .call(done);
    });

    afterEach(function(done) {
      client.end(done);
    });
});
```
Para ejecutar la prueba se deberá ejecutar el siguiente comando:
```
gulp
```

Eso es todo lo que tengo que decir al respecto!