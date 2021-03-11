<!-- .slide: data-auto-animate -->
<h2 data-id="webpack-mf-title">Module Federation</h2>
<ul>
<li>Нововведение в Webpack 5</li>
<li class="fragment">Придумал <a href="https://twitter.com/ScriptedAlchemy">Zack Jackson</a></li>
<li class="fragment">Дает возможность подключать динамически модули из другой webpack-сборки, которые могут быть на другом хосте</li>
<li class="fragment">Иными словами MF позволяет использовать микрофронтенды прямо в браузере 🎉</li>
</ul>
-----
<!-- .slide: data-auto-animate -->
<h2 data-id="webpack-mf-title">Для чего был придуман</h2>
<ul>
    <li>Безболезненно разрабатывать большое приложение несколькими командами</li>
    <li class="fragment">Убрать перезагрузку страниц при переходе между Microfrontend'ами</li>
    <li class="fragment">Не грузить лишний раз vendor code, который уже был загружен ранее</li>
    <li class="fragment">Не надо пересобирать приложение, если поменялся один из Microfrontend</li>
    <li class="fragment">Приложение собирается налету в браузере, а удаленные модули можно просто разместить на CDN</li>
</ul>
-----
<!-- .slide: data-auto-animate -->
<h2 data-id="webpack-mf-title">Tobias Koppers / Zack Jackson</h2>
<div class="r-stack">
    <img style="width: 100%; max-width: 800px; height: auto;" src="slides/08-webpack-mf/motivation.png" alt="module federation motivation">
</div>
<p style="font-size: 0.75em"><a href="https://www.youtube.com/watch?v=gmUm7CTsNhk">Module Federation in Webpack 5 - Tobias Koppers [Youtube]</a></p>
<p style="font-size: 0.75em"><a href="https://www.youtube.com/watch?v=-ei6RqZilYI">Webpack 5 Module Federation - Zack Jackson [Youtube]</a></p>

Note:
На слайде приведены мотивации при создании MF
-----
<!-- .slide: data-auto-animate -->
<h2 data-id="webpack-mf-title">Простая диаграмма</h2>
<div class="r-stack">
    <img style="width: 100%; max-width: 800px; height: auto;" src="slides/08-webpack-mf/simple-diagram.png" alt="module federation diagram">
</div>
<p>Tobias Koppers</p>

Note:
Application - Host
Remote - создает container,
Container - может быть и у Host
-----
<h2 data-id="webpack-mf-title">Типы Bundle</h2>
<ul>
    <li>Host – bundle, который первый инициализировался во время загрузки страницы</li>
    <li class="fragment">Remote – bundle, у которого есть экспортируемые модули</li>
    <li class="fragment">Bidirectional – bundle, который может быть Host или Remote</li>
    <li class="fragment">Omnidirectional – bundle, который одновременно может быть и Host, и Remote</li>
</ul>
-----
<h2 data-id="webpack-mf-title">Типы Modules</h2>
<ul>
    <li>Exposed – модули, которые доступны для импорта другим приложениям</li>
    <li class="fragment">Shared – общие vendor-модули (React, ...)</li>
</ul>
-----
<h2 data-id="webpack-mf-title">Подробная диаграмма</h2>
<div class="r-stack">
    <img style="background: whitesmoke; width: 100%; max-width: 700px; height: auto;" src="slides/08-webpack-mf/diagram.svg" alt="module federation diagram">
</div>

[Павел Черторогов(nodkz) доклад про Module Federation](https://nodkz.github.io/conf-talks/talks/2020.10.26-webpack-federation/#/)

Note:
Пользователь заходит на сайт, подгружаются все remoteEntry, вызов приложения асинхронный, 
наполняются scop'ы shared deps, score смотрит все ли есть для загрузки кнопки и отдает промис, 
который мы используем у себя в App
-----
<h2 data-id="webpack-mf-title">Демо</h2>

[sw1tchdev/meetup-webpack-mf](https://github.com/sw1tchdev/meetup-webpack-mf)

Note:
Я сначала продемонстрирую демо, потом расскажу про настройки
-----
<!-- .slide: data-auto-animate -->
<h2 data-id="webpack-mf-title">HomeApp Config</h2>
<p data-id="webpack-mf-filename" class="reveal r-hstack justify-start">host webpack.config.js:</p>
<pre data-id="webpack-mf-animation"><code class="javascript" data-trim data-line-numbers="|7-8|13|16-41|17|18|19-22|23-26|27-40">const { ModuleFederationPlugin } = require('webpack').container;
// ...
module.exports = {
    entry: path.resolve(__dirname, 'src/index.js'),
    // ...
    devServer: {
        host: '0.0.0.0',
        port: 9001,
        // ...
    },
    output: {
        // ...
        publicPath: 'auto',
    },
    plugins: [
        new ModuleFederationPlugin({
            name: 'host',
            filename: "remoteEntry.js",
            remotes: {
                about: "about@//localhost:9003/remoteEntry.js",
                button: "button@//localhost:9002/remoteEntry.js",
            },
            exposes: {
                "./Navigation": "./src/Navigation",
                "./routes": "./src/routes",
            },
            shared: {
                react: {
                    requiredVersion: '^17.0.1',
                    singleton: true,
                },
                'react-dom': {
                    requiredVersion: '^17.0.1',
                    singleton: true,
                },
                'react-router-dom': {
                      requiredVersion: '^5.2.0',
                      singleton: true,
                }
            },
        }),
        // ...
    ],
};
</code></pre>

-----
<h2 data-id="webpack-mf-title">AboutApp Config</h2>
<p data-id="webpack-mf-filename" class="reveal r-hstack justify-start">host webpack.config.js:</p>
<pre data-id="webpack-mf-animation"><code class="javascript" data-trim data-line-numbers="|7-8|13|16-41|17|18|19-22|23-25|26-39">const { ModuleFederationPlugin } = require('webpack').container;
// ...
module.exports = {
    entry: path.resolve(__dirname, 'src/index.js'),
    // ...
    devServer: {
        host: '0.0.0.0',
        port: 9003,
        // ...
    },
    output: {
        // ...
        publicPath: 'auto',
    },
    plugins: [
        new ModuleFederationPlugin({
            name: 'about',
            filename: "remoteEntry.js",
            remotes: {
                home: "home@//localhost:9001/remoteEntry.js",
                button: "button@//localhost:9002/remoteEntry.js",
            },
            exposes: {
                "./routes": "./src/routes",
            },
            shared: {
                react: {
                    requiredVersion: '^17.0.1',
                    singleton: true,
                },
                'react-dom': {
                    requiredVersion: '^17.0.1',
                    singleton: true,
                },
                'react-router-dom': {
                    requiredVersion: '^5.2.0',
                    singleton: true,
                }
            },
        }),
        // ...
    ],
};
</code></pre>
-----
<h2 data-id="webpack-mf-title">ButtonApp Config</h2>
<p data-id="webpack-mf-filename" class="reveal r-hstack justify-start">remote webpack.config.js:</p>
<pre data-id="webpack-mf-animation"><code class="javascript" data-trim data-line-numbers="|7-8|13|16-33|17|18|19|20-22|23-32">const { ModuleFederationPlugin } = require('webpack').container;
// ...
module.exports = {
    entry: path.resolve(__dirname, 'src/index.js'),
    // ...
    devServer: {
        host: '0.0.0.0',
        port: 9002,
        // ...
    },
    output: {
        // ...
        publicPath: 'auto',
    },
    plugins: [
        new ModuleFederationPlugin({
            name: 'button',
            // library: { type: 'var', name: 'button' },
            filename: 'remoteEntry.js',
            exposes: {
                './Button': './src/expose/Button',
            },
            shared: {
                react: {
                    requiredVersion: '^17.0.1',
                    singleton: true,
                },
                'react-dom': {
                    requiredVersion: '^17.0.1',
                    singleton: true,
                },
            },
        }),
        // ...
    ],
};
</code></pre>
-----
<h2 data-id="webpack-mf-title">Недостатки</h2>
<ul>
    <li>Документация MF на <a href="https://webpack.js.org/concepts/module-federation/">webpack.js.org</a></li>
    <li class="fragment">Возможные проблемы с сетью во время загрузки модулей</li>
    <li class="fragment">Сложности с тестированием</li>
    <li class="fragment">SSR можно, но пока это больно</li>
    <li class="fragment">Технология еще не отработана</li>
</ul>
-----
<h2 data-id="webpack-mf-title">Module Federation Dashboard</h2>

[@module-federation/dashboard-plugin](https://www.npmjs.com/package/@module-federation/dashboard-plugin)

<div class="r-stack">
    <img style="background: whitesmoke; width: 100%; max-width: 700px; height: auto;" src="slides/08-webpack-mf/mf-dashboard.png" alt="module federation dashboard">
</div>

-----
<h2 data-id="webpack-mf-title">Примеры Module Federation</h2>

[module-federation/module-federation-examples](https://github.com/module-federation/module-federation-examples)

