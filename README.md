VTEX-CMS-Sauce
==============
VTEX package for handling CMS requests
--------------------------------------
*This package is not official and may be deprecated as a result of any future changes to the VTEX portal. Before that, use it freely and wisely.*

## :warning: Warning! :warning:
*VTEX-CMS-Sauce was created to allow a better deploying experience for VTEX shops. Therefore, it's recommended to use it in a brand new environment, to minimize the risk of overwriting existing templates. The chances are almost null, but I thought I should warn you.*

### Getting started
1. `npm install vtex-cms-sauce`.

2. Create a `.vtexid` config file in project root with the following content:
```
{
  "login": "your-email@something.com",
  "password": "your-password"
}
```
*NOTE: You must have access to the store you're trying to work on. Also, you should use your VTEX login and password, not Google/Facebook.*

3. Create your api-sauced cms handler:
```
import { create } from 'vtex-cms-sauce'
const cms = create('https://STORE_NAME.vtexcommercestable.com.br')
...
```

### Methods

#### Save Template
`cms.saveTemplate(templateName, HTML, isSub = false)`
* templateName **{String}** - *From this string, the templateId will be created in background.*
* HTML **{String}** - *String containing the HTML template.*
* isSub **{Boolean}** - *Pass true if subtemplate*

###### Example
```
const HTML = `<!DOCTYPE html>
<head>
  <title>VTEX</title>
</head>
<body>
  <h1>Hello, World!</h1>
</body>
`
cms
  .saveTemplate('Home', HTML)
  .then(console.log)
  .catch(console.error)
```

#### Save Shelf Template
`cms.saveShelfTemplate(templateName, HTML, shelfClass)`
* templateName **{String}** - *From this string, the templateId will be created in background.*
* HTML **{String}** - *String containing the HTML template.*
* shelfClass **{String}** - *Classname of shelf container*

###### Example
```
const HTML = `
<div class="product">
  Product
</div>
`
cms
  .saveShelfTemplate('MainShelf', HTML, 'main-shelf')
  .then(console.log)
  .catch(console.error)
```

#### Save File
`cms.saveFile(filepath)`
* filepath **{String}** - *Path of file to be uploaded*

###### Example
```
cms
  .saveFile('/dist/background.png')
  .then(console.log)
  .catch(console.error)
```

### Publish script example
```
const path = require('path'),
    fs = require('fs'),
    create = require('vtex-cms-sauce').create,
    pjson = require('../package.json')

const cms = create('http://examplestore.vtexcommercestable.com.br')
const appDirectory = fs.realpathSync(process.cwd())
const resolveApp = (relativePath) => path.resolve(appDirectory, relativePath)
const templatePrefix = pjson.storeName.replace(/\s/, '').toUpperCase() + '-'

const templatesDir = resolveApp('build/templates')
const subtemplatesDir = resolveApp('build/subtemplates')
const shelvesDir = resolveApp('build/prateleiras')
const filesDir = resolveApp('build/arquivos')

fs.readdirSync(subtemplatesDir)
  .forEach(file => {
    const content = fs.readFileSync(`${subtemplatesDir}/${file}`, 'utf8')
    cms.saveTemplate(templatePrefix + file.replace('.html', ''), content, true)
      .then(console.log)
      .catch(console.error)
  })

fs.readdirSync(shelvesDir)
  .forEach(file => {
    const content = fs.readFileSync(`${shelvesDir}/${file}`, 'utf8')
    cms.saveShelfTemplate(templatePrefix + file.replace('.html', ''), content)
      .then(console.log)
      .catch(console.error)
  });

fs.readdirSync(templatesDir)
  .forEach(file => {
    const content = fs.readFileSync(`${templatesDir}/${file}`, 'utf8')
    cms.saveTemplate(templatePrefix + file.replace('.html', '').replace('index', 'Home'), content)
      .then(console.log)
      .catch(console.error)
  })

fs.readdirSync(filesDir)
  .forEach(file => cms.saveFile(`${filesDir}/${file}`)
                      .then(console.log)
                      .catch(console.error))
```

## License
MIT © [Mauricio Alvim](https://github.com/alvimm)