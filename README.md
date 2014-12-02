## COPYRIGHT
Original work Copyright (c) 2007 Djordje Ungar

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in
all copies or substantial portions of the Software.

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
