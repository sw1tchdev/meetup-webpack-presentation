## Базовая сборка
#### или с какими трудностями можете столкнуться <!-- .element: class="fragment" -->
[sw1tchdev/meetup-webpack](https://github.com/sw1tchdev/meetup-webpack) - репозиторий со сборкой
- JS с ES6+ <!-- .element: class="fragment" -->
- Polyfills <!-- .element: class="fragment" -->
- Sass <!-- .element: class="fragment" -->
- Assets <!-- .element: class="fragment" -->
- Typescript <!-- .element: class="fragment" -->
- ServiceWorkers <!-- .element: class="fragment" -->
- Production <!-- .element: class="fragment" -->
-----
<!-- .slide: data-auto-animate data-menu-title="JS ES6+ 1/2" -->
<h2 data-id="code-title">JS ES6+</h2>
<p data-id="code-filename" class="reveal r-hstack justify-start">npm install:</p>
<pre data-id="code-animation"><code class="bash" data-trim>npm i --save-dev babel-loader @babel/core @babel/preset-env \
@babel/plugin-transform-runtime
npm i --save @babel/runtime
</code></pre>

Note:
preset-env пресет для транспилинга js кода с поддержкой es6+ кода
@babel/plugin-transform-runtime служит для переиспользования кода runtime helper'ов
-----
<!-- .slide: data-auto-animate data-menu-title="JS ES6+ 2/2" -->
<h2 data-id="code-title">JS ES6+</h2>
<p data-id="code-filename" class="reveal r-hstack justify-start">webpack.config.js: </p>
<pre data-id="code-animation"><code class="javascript" data-trim data-line-numbers="|7|8|10-15|16-23|18|20">module.exports = {
    // ...
    module: {
        rules: [
            {
                test: /\.js$/,
                loader: 'babel-loader',
                exclude: /node_modules/,
                options: {
                    babelrc: false, // or configFile: false,
                    presets: [
                        [
                         '@babel/preset-env',
                        ],
                    ],
                    plugins: [
                        [
                            '@babel/plugin-transform-runtime',
                            {
                                helpers: true,
                            },
                        ],
                    ],
                },
            },
        ],
    },
}
</code></pre>
-----
<!-- .slide: data-auto-animate data-menu-title="About Config Files" -->
<h2 data-id="code-title">About Config Files</h2>
<ul>
    <li>Babel (.babelrc, babel.config.js, ...) - <a href="https://babeljs.io/docs/en/config-files">docs</a></li>
    <li class="fragment">Eslint (.eslintrc, ...) - <a href="https://eslint.org/docs/user-guide/configuring/configuration-files">docs</a></li>
</ul>

Note:
Учитывайте, что babel еще может использоваться для jest'a, eslint'a, и может быть еще использоваться для node транспила
Так же возможен merging конфигов
-----
<!-- .slide: data-menu-title="Polyfills 1/5" -->
<h2 data-id="code-title">Polyfills</h2>
<p data-id="code-filename" class="reveal r-hstack justify-start">npm install:</p>
<pre data-id="code-animation"><code class="bash" data-trim>npm i --save core-js @babel/runtime regenerator-runtime
</code></pre>
-----
<!-- .slide: data-auto-animate data-menu-title="Polyfills 2/5" -->
<h2 data-id="code-title">Polyfills</h2>
<p data-id="code-filename" class="reveal r-hstack justify-start">package.json:</p>
<pre data-id="code-animation"><code class="json" data-trim>{
//...
 "browserslist": [
    "> 0.5%",
    "last 2 versions",
    "Firefox ESR",
    "not dead"
  ],
}
</code></pre>

Note:
Firefox ESR - enterprise
больше 0.5% пользователей
-----
<!-- .slide: data-auto-animate data-menu-title="Polyfills 3/5" -->
<h2 data-id="code-title">Polyfills</h2>
<p data-id="code-filename" class="reveal r-hstack justify-start">webpack.config.js: </p>
<pre data-id="code-animation"><code class="javascript" data-trim data-line-numbers="|14-17|15|16|24">module.exports = {
    // ...
    module: {
        rules: [
            {
                test: /\.js$/,
                loader: 'babel-loader',
                exclude: /node_modules/,
                options: {
                    babelrc: false,
                    presets: [
                        [
                            '@babel/preset-env',
                            {
                                useBuiltIns: 'entry', // usage
                                corejs: '3.8',
                            },
                        ],
                    ],
                    plugins: [
                        [
                            '@babel/plugin-transform-runtime',
                            {
                                // corejs: 3,
                                helpers: true,
                            },
                        ],
                    ],
                },
            },
        ],
    },
}
</code></pre>
-----
<!-- .slide: data-menu-title="Polyfills 4/5" -->
<h2 data-id="code-title">Polyfills</h2>
<p data-id="code-filename" class="reveal r-hstack justify-start">entrypoint.js:</p>
<pre data-id="code-animation"><code class="javascript" data-trim>import 'core-js/stable'; // or core-js for all polyfills
import 'regenerator-runtime/runtime';
</code></pre>
-----
<!-- .slide: data-auto-animate data-menu-title="Polyfills 5/5" -->
<h2 data-id="code-title">Polyfills (with usage)</h2>
<p data-id="code-filename" class="reveal r-hstack justify-start">webpack.config.js: </p>
<pre data-id="code-animation"><code class="javascript" data-trim data-line-numbers="|22|9-14|17">const { dependencies } = require('./package.json');
module.exports = {
    // ...
    module: {
        rules: [
            {
                test: /\.js$/,
                loader: 'babel-loader',
                exclude: file => {
                    const filteredDependencies = Object.keys(dependencies)
                        .filter((value) => !value.includes('core-js'))
                        .map((value) => `node_modules/${value}`);
                    return /node_modules/.test(file) && !filteredDependencies.some((value) => file.includes(value));
                },
                options: {
                    babelrc: false,
                    sourceType: 'unambiguous',
                    presets: [
                        [
                            '@babel/preset-env',
                            {
                                useBuiltIns: 'usage',
                                corejs: '3.8',
                            },
                        ],
                    ],
                    plugins: [
                        [
                            '@babel/plugin-transform-runtime',
                            {
                                helpers: true, // or corejs for polyfills
                            },
                        ],
                    ],
                },
            },
        ],
    },
}
</code></pre>
<p class="reveal fragment r-hstack justify-start">🧐&nbsp;<a href="https://github.com/zloirock/core-js/blob/master/docs/2019-03-19-core-js-3-babel-and-a-look-into-the-future.md#babelruntime-for-target-environment">core-js info</a>&nbsp;/&nbsp;<a href="https://babeljs.io/docs/en/babel-preset-env#usebuiltins">preset-env info</a></p>
-----
<!-- .slide: data-menu-title="Sass 1/2" -->
<h2 data-id="code-title">Sass</h2>
<p data-id="code-filename" class="reveal r-hstack justify-start">npm install:</p>
<pre data-id="code-animation"><code class="bash" data-trim>npm i --save-dev css-loader mini-css-extract-plugin \
postcss postcss-loader autoprefixer sass sass-loader 
</code></pre>
<p class="reveal fragment r-hstack justify-start">🧐 node-sass is deprecated</p>
<p class="reveal fragment r-hstack justify-start">👆 можно еще использовать style-loader для inline-стилей</p>
-----
<!-- .slide: data-auto-animate data-menu-title="Sass 2/2" -->
<h2 data-id="code-title">Sass</h2>
<p data-id="code-filename" class="reveal r-hstack justify-start">webpack.config.js: </p>
<pre data-id="code-animation"><code class="javascript" data-trim data-line-numbers="|8-23|9-12">module.exports = {
    // ...
    module: {
        rules: [
          {
            test: /\.(scss|css)$/,
            use: [
              MiniCssExtractPlugin.loader,
              {
                loader: 'css-loader',
                options: { importLoaders: 1 },
              },
              {
                loader: "postcss-loader",
                options: {
                  postcssOptions: {
                    plugins: [
                      "autoprefixer",
                    ],
                  },
                },
              },
              'sass-loader',
            ],
          },
        ],
    },
    plugins: [
        new MiniCssExtractPlugin({
            filename: '[name].css',
            chunkFilename: '[id].css',
        }),
    ],
}
</code></pre>
-----
<h2 data-id="code-title">Assets</h2>
<p data-id="code-filename" class="reveal r-hstack justify-start">webpack.config.js: </p>
<pre data-id="code-animation"><code class="javascript" data-trim data-line-numbers="|7|11">module.exports = {
    // ...
    module: {
        rules: [
            {
                test: /\.(ico|gif|png|jpg|jpeg)$/i,
                type: 'asset/resource',
            },
            {
                test: /\.(woff(2)?|eot|ttf|otf|svg)$/,
                type: 'asset/inline',
            },
        ],
    },
}
</code></pre>
-----
<!-- .slide: data-menu-title="Typescript 1/3" -->
<h2 data-id="code-title">Typescript</h2>
<p data-id="code-filename" class="reveal r-hstack justify-start">npm install:</p>
<pre data-id="code-animation"><code class="bash" data-trim>npm i --save-dev typescript @babel/preset-typescript fork-ts-checker-webpack-plugin
</code></pre>
<p class="reveal fragment r-hstack justify-start">🧐 если надо генерить тайпинги, то ts-loader</p>
<p class="reveal fragment r-hstack justify-start">👆или использовать tsc&nbsp;<a href="https://www.typescriptlang.org/docs/handbook/babel-with-typescript.html">[typescriptlang.org]</a></p>
-----
<!-- .slide: data-auto-animate data-menu-title="Typescript 2/3" -->
<h2 data-id="code-title">Typescript</h2>
<p data-id="code-filename" class="reveal r-hstack justify-start">tsconfig.json:</p>
<pre data-id="code-animation"><code class="json" data-trim>{
      "compilerOptions": {
        "target": "ES2020",         /* Specify ECMAScript target version: 'ES3' (default), 'ES5', 'ES2015', 'ES2016', 'ES2017', 'ES2018', 'ES2019', 'ES2020', or 'ESNEXT'. */
        "module": "es2020",         /* Specify module code generation: 'none', 'commonjs', 'amd', 'system', 'umd', 'es2015', 'es2020', or 'ESNext'. */
        "declaration": true,        /* Generates corresponding '.d.ts' file. */
        "declarationDir": "dist",   /* Generates corresponding '.d.ts' file. */
        "strict": true,             /* Enable all strict type-checking options. */
        "esModuleInterop": true,    /* Enables emit interoperability between CommonJS and ES Modules via creation of namespace objects for all imports. Implies 'allowSyntheticDefaultImports'. */
        "isolatedModules": true,
        "skipLibCheck": true,       /* Skip type checking of declaration files. */
        "forceConsistentCasingInFileNames": true  /* Disallow inconsistently-cased references to the same file. */
    }
}
</code></pre>

Note:
isolatedModules - флаг говорит TypeScript, что ваш код может быть неправильно интерпретирован транспилингом, которым преобразовывает файлы одиночно.
-----
<!-- .slide: data-auto-animate data-menu-title="Typescript 3/3" -->
<h2 data-id="code-title">Typescript</h2>
<p data-id="code-filename" class="reveal r-hstack justify-start">webpack.config.js:</p>
<pre data-id="code-animation"><code class="javascript" data-trim data-line-numbers="|4|9|22|36-38">module.exports = {
    // ...
    resolve: {
        extensions: ['.js', '.json', '.ts'],
    },
    module: {
        rules: [
          {
            test: /\.(js|ts)$/,
            loader: 'babel-loader',
            exclude: /node_modules/,
            options: {
              babelrc: false,
              presets: [
                [
                  '@babel/preset-env',
                  {
                    useBuiltIns: 'entry',
                    corejs: '3.8',
                  },
                ],
                '@babel/preset-typescript',
              ],
              plugins: [
                [
                  '@babel/plugin-transform-runtime',
                  {
                    helpers: true,
                  },
                ],
              ],
            },
          },
        ],
    },
    plugins: [
        new ForkTsCheckerWebpackPlugin()
    ],
}
</code></pre>
-----
<!-- .slide: data-menu-title="Dev Server (HMR) 1/2" -->
<h2 data-id="code-title">Dev Server (HMR)</h2>
<p data-id="code-filename" class="reveal r-hstack justify-start">npm install:</p>
<pre data-id="code-animation"><code class="bash" data-trim>npm i --save-dev webpack-dev-server
</code></pre>
<p class="reveal fragment r-hstack justify-start">🧐&nbsp;<a href="https://github.com/webpack/webpack-dev-server/releases/tag/v4.0.0-beta.0">v4.0.0-beta.0</a></p>

Note:
Cервер для разработки, который обновляет браузер или заменяет обновившийся модуль в сборке (HMR) при каждом изменении приложение
-----
<!-- .slide: data-menu-title="Dev Server (HMR) 2/2" -->
<h2 data-id="code-title">Dev Server (HMR)</h2>
<p data-id="code-filename" class="reveal r-hstack justify-start">webpack.config.js:</p>
<pre data-id="code-animation"><code class="javascript" data-trim data-line-numbers="|9-10|11|12">module.exports = {
    //...
    devServer: {
          host: '0.0.0.0',
          port: 9000,
          publicPath: '/',
          contentBase: path.join(PROJECT_DIR, 'dist'),
          disableHostCheck: true,
          watchContentBase: true, // watching in build folder (for example html)
          liveReload: true,
          // hot: true,
          injectClient: true // must specify if browserslist config exists and webpack target not specified
    },
}
</code></pre>

Note:
в 4 версии нормально отображается цель у билда, в 3 версии он определяется как browserslist и не инжектит код
-----
<!-- .slide: data-auto-animate data-menu-title="Service Worker 1/3" -->
<h2 data-id="code-title">Service Worker</h2>
<p data-id="code-filename" class="reveal r-hstack justify-start">webpack.config.js:</p>
<pre data-id="code-animation"><code class="javascript" data-trim data-trim data-line-numbers="|16-24|13">
// multiple targets
module.exports = [
{
    // ... target 1
    devServer: {
          host: '0.0.0.0',
          port: 9000,
          publicPath: '/',
          contentBase: path.join(PROJECT_DIR, 'dist'),
          watchContentBase: true,
          disableHostCheck: true,
          liveReload: true, // or hot
          injectClient: (compilerConfig) => compilerConfig.target === 'browserslist', // or compare by compilerConfig.entry
        },
},
{
    //... target 2
    target: 'webworker',
    entry: path.resolve(PROJECT_DIR, 'src/sw.js'),
    output: {
        filename: 'sw.js',
        path: path.resolve(PROJECT_DIR, 'dist'),
    },
}
]
</code></pre>
<p class="reveal fragment r-hstack justify-start">🧐&nbsp;Если multiple targets, то учитывайте CleanWebpackPlugin</p>
-----
<!-- .slide: data-menu-title="Service Worker 2/3"-->
<h2 data-id="code-title">Service Worker</h2>
<p data-id="code-filename" class="reveal r-hstack justify-start">webpack.config.js:</p>
<pre data-id="code-animation"><code class="javascript" data-trim data-trim data-line-numbers="10">
module.exports = {
    devServer: {
          host: '0.0.0.0',
          port: 9000,
          publicPath: '/',
          contentBase: path.join(PROJECT_DIR, 'dist'),
          watchContentBase: true,
          disableHostCheck: true,
          liveReload: true, // or hot
          injectClient: true
    },
}
</code></pre>
-----
<!-- .slide: data-menu-title="Service Worker 3/3"-->
<h2 data-id="code-title">Service Worker</h2>
<p data-id="code-filename" class="reveal r-hstack justify-start">entrypoint.js:</p>
<pre data-id="code-animation"><code class="javascript" data-trim data-trim>
navigator.serviceWorker.register(new URL('./sw.js', import.meta.url))
</code></pre>
<p class="reveal fragment r-hstack justify-start">😎&nbsp;Webpack сам создаст новый entrypoint для SW</p>
-----
<!-- .slide: data-auto-animate data-menu-title="Production 1/2" -->
<h2 data-id="code-title">Production</h2>
<p data-id="code-filename" class="reveal r-hstack justify-start">npm install:</p>
<pre data-id="code-animation"><code class="bash" data-trim>npm i --save-dev terser-webpack-plugin \
css-minimizer-webpack-plugin eslint-webpack-plugin \
stylelint stylelint-webpack-plugin webpack-bundle-analyzer
</code></pre>
-----
<!-- .slide: data-menu-title="Production 2/2" -->
<h2 data-id="code-title">Production</h2>
<p data-id="code-filename" class="reveal r-hstack justify-start">webpack.config.js:</p>
<pre data-id="code-animation"><code class="javascript" data-trim data-trim data-line-numbers="|4-6|8|9-16|20-28">
module.exports = {
    // ...
    optimization: {
        splitChunks: {
            chunks: 'all',
        },
        minimizer: [
            new CssMinimizerPlugin(),
            new TerserPlugin({
                terserOptions: {
                    output: {
                        comments: false,
                    },
                },
                extractComments: false,
            }),
        ],
    },
    plugins: [
        new ESLintPlugin(),
        new StylelintWebpackPlugin({
                files: 'path/to/src/**/*.(s(c|a)ss|css)' // or you can specify webpack context
            }),
        // or you can pass --analyze flag to webpack-cli
        new BundleAnalyzerPlugin({
                openAnalyzer: true,
                analyzerPort: 'auto',
            }),
    ],
}
</code></pre>
