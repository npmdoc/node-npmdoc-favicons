# api documentation for  [favicons (v4.8.3)](https://github.com/haydenbleasel/favicons)  [![npm package](https://img.shields.io/npm/v/npmdoc-favicons.svg?style=flat-square)](https://www.npmjs.org/package/npmdoc-favicons) [![travis-ci.org build-status](https://api.travis-ci.org/npmdoc/node-npmdoc-favicons.svg)](https://travis-ci.org/npmdoc/node-npmdoc-favicons)
#### Favicon generator for Node.js

[![NPM](https://nodei.co/npm/favicons.png?downloads=true)](https://www.npmjs.com/package/favicons)

[![apidoc](https://npmdoc.github.io/node-npmdoc-favicons/build/screenCapture.buildNpmdoc.browser._2Fhome_2Ftravis_2Fbuild_2Fnpmdoc_2Fnode-npmdoc-favicons_2Ftmp_2Fbuild_2Fapidoc.html.png)](https://npmdoc.github.io/node-npmdoc-favicons/build/apidoc.html)

![npmPackageListing](https://npmdoc.github.io/node-npmdoc-favicons/build/screenCapture.npmPackageListing.svg)

![npmPackageDependencyTree](https://npmdoc.github.io/node-npmdoc-favicons/build/screenCapture.npmPackageDependencyTree.svg)



# package.json

```json

{
    "author": {
        "name": "Hayden Bleasel",
        "email": "haydenbleasel@gmail.com"
    },
    "bugs": {
        "url": "https://github.com/haydenbleasel/favicons/issues"
    },
    "dependencies": {
        "async": "^1.5.0",
        "cheerio": "^0.19.0",
        "clone": "^1.0.2",
        "colors": "^1.1.2",
        "harmony-reflect": "^1.4.2",
        "image-size": "^0.4.0",
        "jimp": "^0.2.13",
        "jsontoxml": "0.0.11",
        "merge-defaults": "^0.2.1",
        "mkdirp": "^0.5.1",
        "node-rest-client": "^1.5.1",
        "require-directory": "^2.1.1",
        "svg2png": "~3.0.1",
        "through2": "^2.0.0",
        "tinycolor2": "^1.1.2",
        "to-ico": "^1.1.2",
        "underscore": "^1.8.3",
        "vinyl": "^1.1.0"
    },
    "description": "Favicon generator for Node.js",
    "devDependencies": {
        "babel-preset-es2015": "^6.1.18",
        "eslint": "^2.3.0",
        "gulp": "^3.9.0",
        "gulp-babel": "^6.1.1",
        "gulp-if": "^2.0.0",
        "gulp-rename": "^1.2.2",
        "gutil": "^1.6.4"
    },
    "directories": {},
    "dist": {
        "shasum": "c7a2ccd9a5667baf2c01bbe5c78f7564457a7693",
        "tarball": "https://registry.npmjs.org/favicons/-/favicons-4.8.3.tgz"
    },
    "gitHead": "aca10822327705c681a9902e711cc198f8096658",
    "homepage": "https://github.com/haydenbleasel/favicons",
    "keywords": [
        "favicon",
        "ico",
        "generator",
        "node",
        "realfavicongenerator",
        "gulpfriendly"
    ],
    "license": "ISC",
    "main": "index.js",
    "maintainers": [
        {
            "name": "haydenbleasel",
            "email": "haydenbleasel@gmail.com"
        }
    ],
    "name": "favicons",
    "optionalDependencies": {},
    "readme": "ERROR: No README data found!",
    "repository": {
        "type": "git",
        "url": "git+https://github.com/haydenbleasel/favicons.git"
    },
    "scripts": {
        "prepublish": "gulp",
        "test": "cd test && node test.js && gulp"
    },
    "version": "4.8.3"
}
```



# <a name="apidoc.tableOfContents"></a>[table of contents](#apidoc.tableOfContents)

#### [module favicons](#apidoc.module.favicons)
1.  [function <span class="apidocSignatureSpan">favicons.</span>es5 (source, parameters, next)](#apidoc.element.favicons.es5)
1.  [function <span class="apidocSignatureSpan">favicons.</span>stream (params, handleHtml)](#apidoc.element.favicons.stream)
1.  object <span class="apidocSignatureSpan">favicons.</span>config

#### [module favicons.es5](#apidoc.module.favicons.es5)
1.  [function <span class="apidocSignatureSpan">favicons.</span>es5 (source, parameters, next)](#apidoc.element.favicons.es5.es5)
1.  [function <span class="apidocSignatureSpan">favicons.es5.</span>stream (params, handleHtml)](#apidoc.element.favicons.es5.stream)
1.  object <span class="apidocSignatureSpan">favicons.es5.</span>config



# <a name="apidoc.module.favicons"></a>[module favicons](#apidoc.module.favicons)

#### <a name="apidoc.element.favicons.es5"></a>[function <span class="apidocSignatureSpan">favicons.</span>es5 (source, parameters, next)](#apidoc.element.favicons.es5)
- description and source-code
```javascript
function favicons(source, parameters, next) {

    var config = clone(configDefaults),
        options = _.mergeDefaults(parameters || {}, config.defaults),
        µ = helpers(options),
        background = µ.General.background(options.background);

    function createFavicon(sourceset, properties, name, platformOptions, callback) {
        if (path.extname(name) === '.ico') {
            async.map(properties.sizes, function (sizeProperties, cb) {
                var newProperties = clone(properties);

                newProperties.width = sizeProperties.width;
                newProperties.height = sizeProperties.height;

                var tempName = 'favicon-temp-' + newProperties.width + 'x' + newProperties.height + '.png';

                createFavicon(sourceset, newProperties, tempName, platformOptions, cb);
            }, function (error, results) {
                if (error) {
                    return callback(error);
                }

                var files = results.map(function (icoImage) {
                    return icoImage.contents;
                });

                toIco(files).then(function (buffer) {
                    return callback(null, { name: name, contents: buffer });
                }).catch(callback);
            });
        } else {
            (function () {
                var maximum = Math.max(properties.width, properties.height),
                    offset = Math.round(maximum / 100 * platformOptions.offset) || 0;

                async.waterfall([function (cb) {
                    return µ.Images.nearest(sourceset, properties, offset, cb);
                }, function (nearest, cb) {
                    return µ.Images.read(nearest.file, cb);
                }, function (buffer, cb) {
                    return µ.Images.resize(buffer, properties, offset, cb);
                }, function (resizedBuffer, cb) {
                    return µ.Images.create(properties, background, function (error, canvas) {
                        return cb(error, resizedBuffer, canvas);
                    });
                }, function (resizedBuffer, canvas, cb) {
                    return µ.Images.composite(canvas, resizedBuffer, properties, offset, maximum, cb);
                }, function (composite, cb) {
                    µ.Images.getBuffer(composite, cb);
                }], function (error, buffer) {
                    return callback(error, { name: name, contents: buffer });
                });
            })();
        }
    }

    function createHTML(platform, callback) {
        var html = [];

        async.forEachOf(config.html[platform], function (tag, selector, cb) {
            return µ.HTML.parse(tag, function (error, metadata) {
                return cb(html.push(metadata) && error);
            });
        }, function (error) {
            return callback(error, html);
        });
    }

    function createFiles(platform, callback) {
        var files = [];

        async.forEachOf(config.files[platform], function (properties, name, cb) {
            return µ.Files.create(properties, name, function (error, file) {
                return cb(files.push(file) && error);
            });
        }, function (error) {
            return callback(error, files);
        });
    }

    function createFavicons(sourceset, platform, platformOptions, callback) {
        var images = [];

        async.forEachOf(config.icons[platform], function (properties, name, cb) {
            return createFavicon(sourceset, properties, name, platformOptions, function (error, image) {
                return cb(images.push(image) && error);
            });
        }, function (error) {
            return callback(error, images);
        });
    }

    function createPlatform(sourceset, platform, platformOptions, callback) {
        async.parallel([function (cb) {
            return createFavicons(sourceset, platform, platformOptions, cb);
        }, function (cb) {
            return createFiles(platform, cb);
        }, function (cb) {
            return createHTML(platform, cb);
        }], functi ...
```
- example usage
```shell
n/a
```

#### <a name="apidoc.element.favicons.stream"></a>[function <span class="apidocSignatureSpan">favicons.</span>stream (params, handleHtml)](#apidoc.element.favicons.stream)
- description and source-code
```javascript
function stream(params, handleHtml) {

    const config = clone(configDefaults),
        µ = helpers(params);

    function processDocuments (documents, html, callback) {
        async.each(documents, (document, cb) =>
            µ.HTML.update(document, html, config.html, cb),
        (error) =>
            callback(error));
    }

<span class="apidocCodeCommentSpan">    /* eslint func-names: 0, no-invalid-this: 0 */
</span>    return through2.obj(function (file, encoding, callback) {
        const that = this;

        if (file.isNull()) {
            return callback(null, file);
        }

        if (file.isStream()) {
            return callback(new Error('[gulp-favicons] Streaming not supported'));
        }

        async.waterfall([
            (cb) =>
                favicons(file.contents, params, cb),
            (response, cb) =>
                async.each(response.images.concat(response.files), (image, c) => {
                    that.push(µ.General.vinyl(image));
                    c();
                }, (error) =>
                    cb(error, response)),
            (response, cb) => {
                if (handleHtml) {
                    handleHtml(response.html);
                    return cb(null);
                }
                if (params.html && !params.pipeHTML) {
                    const documents = typeof params.html === 'object' ? params.html : [params.html];

                    processDocuments(documents, response.html, cb);
                } else {
                    return cb(null);
                }
            }
        ], (error) =>
            callback(error));
    });
}
```
- example usage
```shell
n/a
```



# <a name="apidoc.module.favicons.es5"></a>[module favicons.es5](#apidoc.module.favicons.es5)

#### <a name="apidoc.element.favicons.es5.es5"></a>[function <span class="apidocSignatureSpan">favicons.</span>es5 (source, parameters, next)](#apidoc.element.favicons.es5.es5)
- description and source-code
```javascript
function favicons(source, parameters, next) {

    var config = clone(configDefaults),
        options = _.mergeDefaults(parameters || {}, config.defaults),
        µ = helpers(options),
        background = µ.General.background(options.background);

    function createFavicon(sourceset, properties, name, platformOptions, callback) {
        if (path.extname(name) === '.ico') {
            async.map(properties.sizes, function (sizeProperties, cb) {
                var newProperties = clone(properties);

                newProperties.width = sizeProperties.width;
                newProperties.height = sizeProperties.height;

                var tempName = 'favicon-temp-' + newProperties.width + 'x' + newProperties.height + '.png';

                createFavicon(sourceset, newProperties, tempName, platformOptions, cb);
            }, function (error, results) {
                if (error) {
                    return callback(error);
                }

                var files = results.map(function (icoImage) {
                    return icoImage.contents;
                });

                toIco(files).then(function (buffer) {
                    return callback(null, { name: name, contents: buffer });
                }).catch(callback);
            });
        } else {
            (function () {
                var maximum = Math.max(properties.width, properties.height),
                    offset = Math.round(maximum / 100 * platformOptions.offset) || 0;

                async.waterfall([function (cb) {
                    return µ.Images.nearest(sourceset, properties, offset, cb);
                }, function (nearest, cb) {
                    return µ.Images.read(nearest.file, cb);
                }, function (buffer, cb) {
                    return µ.Images.resize(buffer, properties, offset, cb);
                }, function (resizedBuffer, cb) {
                    return µ.Images.create(properties, background, function (error, canvas) {
                        return cb(error, resizedBuffer, canvas);
                    });
                }, function (resizedBuffer, canvas, cb) {
                    return µ.Images.composite(canvas, resizedBuffer, properties, offset, maximum, cb);
                }, function (composite, cb) {
                    µ.Images.getBuffer(composite, cb);
                }], function (error, buffer) {
                    return callback(error, { name: name, contents: buffer });
                });
            })();
        }
    }

    function createHTML(platform, callback) {
        var html = [];

        async.forEachOf(config.html[platform], function (tag, selector, cb) {
            return µ.HTML.parse(tag, function (error, metadata) {
                return cb(html.push(metadata) && error);
            });
        }, function (error) {
            return callback(error, html);
        });
    }

    function createFiles(platform, callback) {
        var files = [];

        async.forEachOf(config.files[platform], function (properties, name, cb) {
            return µ.Files.create(properties, name, function (error, file) {
                return cb(files.push(file) && error);
            });
        }, function (error) {
            return callback(error, files);
        });
    }

    function createFavicons(sourceset, platform, platformOptions, callback) {
        var images = [];

        async.forEachOf(config.icons[platform], function (properties, name, cb) {
            return createFavicon(sourceset, properties, name, platformOptions, function (error, image) {
                return cb(images.push(image) && error);
            });
        }, function (error) {
            return callback(error, images);
        });
    }

    function createPlatform(sourceset, platform, platformOptions, callback) {
        async.parallel([function (cb) {
            return createFavicons(sourceset, platform, platformOptions, cb);
        }, function (cb) {
            return createFiles(platform, cb);
        }, function (cb) {
            return createHTML(platform, cb);
        }], functi ...
```
- example usage
```shell
n/a
```

#### <a name="apidoc.element.favicons.es5.stream"></a>[function <span class="apidocSignatureSpan">favicons.es5.</span>stream (params, handleHtml)](#apidoc.element.favicons.es5.stream)
- description and source-code
```javascript
function stream(params, handleHtml) {

    var config = clone(configDefaults),
        µ = helpers(params);

    function processDocuments(documents, html, callback) {
        async.each(documents, function (document, cb) {
            return µ.HTML.update(document, html, config.html, cb);
        }, function (error) {
            return callback(error);
        });
    }

<span class="apidocCodeCommentSpan">    /* eslint func-names: 0, no-invalid-this: 0 */
</span>    return through2.obj(function (file, encoding, callback) {
        var that = this;

        if (file.isNull()) {
            return callback(null, file);
        }

        if (file.isStream()) {
            return callback(new Error('[gulp-favicons] Streaming not supported'));
        }

        async.waterfall([function (cb) {
            return favicons(file.contents, params, cb);
        }, function (response, cb) {
            return async.each(response.images.concat(response.files), function (image, c) {
                that.push(µ.General.vinyl(image));
                c();
            }, function (error) {
                return cb(error, response);
            });
        }, function (response, cb) {
            if (handleHtml) {
                handleHtml(response.html);
                return cb(null);
            }
            if (params.html && !params.pipeHTML) {
                var documents = _typeof(params.html) === 'object' ? params.html : [params.html];

                processDocuments(documents, response.html, cb);
            } else {
                return cb(null);
            }
        }], function (error) {
            return callback(error);
        });
    });
}
```
- example usage
```shell
n/a
```



# misc
- this document was created with [utility2](https://github.com/kaizhu256/node-utility2)
