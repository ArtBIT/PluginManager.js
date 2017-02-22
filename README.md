## Overview

This is a trivially simple plugin system with a local pub-sub mechanism
for inter-plugin communication.

## Example

Let's say you have the following HTML markup:
```html
<div role="toolbar">
    <button role="cut">Cut</button>
    <button role="copy">Copy</button>
    <button role="paste">Paste</button>
</div>
<div role="log"></div>
```

And you have the following plugins:
```js
    // ToolBar Plugin
    function ToolBar(node) {
        this.node = node;
    }
    ToolBar.prototype = new Plugin();
    ToolBar.prototype.constructor = ToolBar;
    ToolBar.prototype.init = function () {
        var onClick = (function (context) {
            return function (e) {
                context.trigger('toolbar:click', this.getAttribute('role'));
            };
        })(this.manager);

        var buttons = this.node.querySelectorAll('button');
        var i = buttons.length;
        while (i--) {
            buttons[i].addEventListener('click', onClick);
        }
    };

    // Console Plugin
    function ConsolePlugin(node) {
        this.node = node;
    }
    ConsolePlugin.prototype = new Plugin();
    ConsolePlugin.prototype.constructor = ConsolePlugin;
    ConsolePlugin.prototype.init = function () {
        var onClick = (function(element) {
            return function (button) {
                var el = document.createElement('p');
                el.innerHTML = 'Clicked button: '+button;
                element.appendChild(el);
            };
        })(this.node);
        this.manager.on('toolbar:click', onClick);
    };
```

You can bind them like this:
```js
    var plugins = new PluginManager();
    plugins.add(new ToolBar(document.querySelector('div[role="toolbar"]')));
    plugins.add(new ConsolePlugin(document.querySelector('div[role="log"]')));
```

You can see it in work by clicking this link: http://jsfiddle.net/ArtBIT/pogr38nt/

## License

See the [LICENSE](LICENSE) file.
