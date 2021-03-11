## Основные принципы
- Entry / Output
- Loaders <!-- .element: class="fragment" -->
- Plugins <!-- .element: class="fragment" -->

-----
<!-- .slide: data-auto-animate -->
<h2 data-id="concept-title">Entry / Output</h2>
<p data-id="concept-filename" class="reveal r-hstack justify-start">webpack.config.js:</p>
<pre data-id="concept-code"><code class="javascript" data-trim data-line-numbers="3-6|7-10|">const path = require('path');
module.exports = {
    entry: {
        outputName: 'path/to/file.js' // or combine with array of files
        outputSecondName: 'path/to/secondFile.js' // or combine with array of files
    },
    output: {
        path: path.resolve(__dirname, 'dist'),
        filename: '[name].[contenthash].bundle.js' // support template strings
    }
}
</code></pre>

Note:
Настроек много, здесь приводится основные для ознакомления
-----
<!-- .slide: data-auto-animate -->
<h2 data-id="concept-title">Loaders</h2>
<p data-id="concept-filename" class="reveal r-hstack justify-start">webpack.config.js:</p>
<pre data-id="concept-code"><code class="javascript" data-trim data-line-numbers="|8">module.exports = {
    // ...
    module: {
        rules: [
            // { test: /\.css$/, loader: 'style-loader' }
            // { test: /\.css$/, loader: 'css-loader' },
            // same as
            { test: /\.css$/i, use: [ 'style-loader', 'css-loader' ] },
        ]
  }
}
</code></pre>

🧐 Порядок выполнения снизу вверх (справа налево) <!-- .element: class="reveal fragment r-hstack justify-start" -->

Note:
Есть еще pitch фаза https://webpack.js.org/api/loaders/#pitching-loader
-----
<!-- .slide: data-auto-animate -->
<h2 data-id="concept-title">Plugins</h2>
<p data-id="concept-filename" class="reveal r-hstack justify-start">webpack.config.js:</p>
<pre data-id="concept-code"><code class="javascript" data-trim data-line-numbers="|6-9">const HtmlWebpackPlugin = require('html-webpack-plugin');
const webpack = require('webpack');
const path = require('path');
module.exports = {
    // ...
    plugins: [
        new webpack.ProgressPlugin(),
        new HtmlWebpackPlugin({template: './src/index.html'})
    ]
}
</code></pre>
<p class="reveal fragment r-hstack justify-start">🧐 Плагины построены на системе хуков -&nbsp;<a href="https://github.com/webpack/tapable">webpack/tapable</a></p>

Note:
Если будете разрабатывать свои плагины, то советую изучить webpack/tapable
