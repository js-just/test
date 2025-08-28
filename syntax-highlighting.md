## test
### test
### test
## test
### test
### test
### test
### test
## test
### test
### test
## test
## test
## test
## test
### test
## test
### test
## test
## test
## test
### test
## test
### test
## test
## test
```js
/*

MIT License

Copyright (c) 2025 JustStudio. <https://juststudio.is-a.dev/>

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.

*/

import { readFileSync, writeFileSync, readdirSync, statSync } from 'fs';
import { join } from 'path';

const deployDir = process.argv[2] || __dirname;
import { JSON as css } from '../lib/ast/css.js';

async function serializeRules(rules) {
    let result = '';

    const ruleToString = async (rule) => {
        if (!rule) return '';

        if (rule.type === 'at-rule') {
            let innerContent = '';
            if (rule.rules && rule.rules.filter(Boolean).length > 0) {
                innerContent = await serializeRules(rule.rules.filter(Boolean).sort((a, b) => a.id - b.id));
            }
            return `${rule.name}{${innerContent}}`;
        } else if (rule.type === 'rule') {
            const props = [];
            for (const [key, value] of Object.entries(rule.properties)) {
                props.push(`${key}:${value}`);
            }
            return `${rule.selectors.join(',')}{${props.join(';')}}`;
        } else if (rule.type === 'insert') {
            return rule.text;
        }
        return '';
    };

    for (const rule of rules) {
        result += await ruleToString(rule);
    }

    return result;
}

async function compressFile(filePath) {
    let content = readFileSync(filePath, 'utf8');
    let done = false;
    if (filePath.endsWith('.css')) {
        content = css(content);
        const compressed = await serializeRules(content.sort((a, b) => a.id - b.id));
        writeFileSync(filePath, compressed, 'utf8');
        done = true;
    } else if (filePath.endsWith('.json') || filePath.endsWith('.webmanifest')) {
        try {
            content = JSON.parse(content);
            const compressed = JSON.stringify(content);
            writeFileSync(filePath, compressed, 'utf8');
            done = true;
        } catch (_e) {}
    }

    if (done) {
        return;
    }

    if (filePath.endsWith('.js')) {
        content = content.replace(/(?<!["'`][\s\S]*)\/\/.*\n/g, '\n')
                         .replace(/\/\*[\s\S]*?\*\//g, '');
    }

    if (filePath.endsWith('.html') || filePath.endsWith('.svg') || filePath.endsWith('.xml')) {
        content = content.replace(/<!--[\s\S]*?-->/g, '');
    }

    if (filePath.endsWith('.js')) {
        content = content
        .replace(/(?<!['"`][\s\S]*)\btrue\b(?!['"`][\s\S]*)/g, '!0')
        .replace(/(?<!['"`][\s\S]*)\bfalse\b(?!['"`][\s\S]*)/g, '!1')
        .replace(/(?<!['"`][\s\S]*)\bundefined\b(?!['"`][\s\S]*)/g, '[][[]]')
        .replace(/(?<!['"`][\s\S]*)\/\/(.*?)\n/g, '');
    }    

    content = content.replace(/(\s*["'`])([^"'\n`]*)(["'`]\s*)/g, (match, p1, p2, p3) => {
        return p1 + p2.replace(/\s+/g, ' ') + p3;
    });

    content = content.replace(/\n\s*/g, ' ').replace(/\s+/g, ' ');

    if (filePath.endsWith('.js')) {
        content = content.replace(/;\s*}/g, '}').replace(/;\s*$/, '')
                         .replace(/(?<!['"`][\s\S]*)\/\*(.*?)\*\/(?!['"`][\s\S]*)/g, '');
    }

    if (filePath.endsWith('.js') || filePath.endsWith('.json') || filePath.endsWith('.webmanifest')) {
        content = content.replace(/,\s*}/g, '}').replace(/,\s*]}/g, ']');
    }

    writeFileSync(filePath, content, 'utf8');
}

async function findAndCompressFiles(dir) {
    await readdirSync(dir).forEach(async file => {
        const filePath = join(dir, file);
        const stat = statSync(filePath);
        if (stat.isDirectory()) {
            await findAndCompressFiles(filePath);
        } else if (
            file.endsWith('.html') || 
            file.endsWith('.svg') || 
            file.endsWith('.xml') || 
            file.endsWith('.css') || 
            file.endsWith('.js') || 
            file.endsWith('.json') || 
            file.endsWith('.webmanifest')
        ) {
            await compressFile(filePath);
        }
    });
}

await findAndCompressFiles(deployDir);

/*

EXAMPLE just.config.js FILE to minify js, css, html files in your static website:

module.exports = {
    type: "compress"
}

*/

```
```sh
# MIT License
# 
# Copyright (c) 2025 JustStudio. <https://juststudio.is-a.dev/>
# 
# Permission is hereby granted, free of charge, to any person obtaining a copy
# of this software and associated documentation files (the "Software"), to deal
# in the Software without restriction, including without limitation the rights
# to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
# copies of the Software, and to permit persons to whom the Software is
# furnished to do so, subject to the following conditions:
# 
# The above copyright notice and this permission notice shall be included in all
# copies or substantial portions of the Software.
# 
# THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
# IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
# FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
# AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
# LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
# OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
# SOFTWARE.

#!/bin/bash
mkdir -p _lastcommit && \
echo "$GITHUB_SHA" > _lastcommit/sha.txt

```
```js
/*

MIT License

Copyright (c) 2025 JustStudio. <https://juststudio.is-a.dev/>

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.

*/

const fs = require('fs');
const path = require('path');
const num_ = require('../lib/number.js');
const [files, id] = process.argv.slice(2);
const filess = JSON.parse(files);
const types = [
    "md", "html", "js", "css"
];
filess.forEach(file => {
    fs.writeFileSync(path.join(process.cwd(), 'demo', `${file.name.replaceAll('.','')}.${types[file.type]}`), file.content, 'utf-8');
});

console.log(num_.convertbase(String(id), 10, 62));

```
```sh
# MIT License
# 
# Copyright (c) 2025 JustStudio. <https://juststudio.is-a.dev/>
# 
# Permission is hereby granted, free of charge, to any person obtaining a copy
# of this software and associated documentation files (the "Software"), to deal
# in the Software without restriction, including without limitation the rights
# to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
# copies of the Software, and to permit persons to whom the Software is
# furnished to do so, subject to the following conditions:
# 
# The above copyright notice and this permission notice shall be included in all
# copies or substantial portions of the Software.
# 
# THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
# IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
# FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
# AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
# LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
# OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
# SOFTWARE.

#!/bin/bash
mkdir -p demo && \
DEMO_LATEST_ID=$(node -e "await fetch('https://raw.just.is-a.dev/v1/demo-id/').then(async resp => {return await resp.json()}).then(resp => {console.log(resp.value)})") && \
DEMO_BUILT_ID=$(node "src/demo.js" "$INPUT_FILES" "$DEMO_LATEST_ID") && \
DEMO_NEW_ID=$(node -e "console.log($DEMO_LATEST_ID + 1)") && \
rm -f "just.config.js" && \
echo "$INPUT_CONFIG" > just.config.js && \
echo "id=id/$DEMO_BUILT_ID" >> $GITHUB_OUTPUT && \
mkdir -p demo-id && \
source lib/tojson.sh && \
CONTENT=$(toJSON "$DEMO_NEW_ID" "Last demo built ID") && \
echo "$CONTENT" > demo-id/index.json

```
## test
```sh
# MIT License
# 
# Copyright (c) 2025 JustStudio. <https://juststudio.is-a.dev/>
# 
# Permission is hereby granted, free of charge, to any person obtaining a copy
# of this software and associated documentation files (the "Software"), to deal
# in the Software without restriction, including without limitation the rights
# to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
# copies of the Software, and to permit persons to whom the Software is
# furnished to do so, subject to the following conditions:
# 
# The above copyright notice and this permission notice shall be included in all
# copies or substantial portions of the Software.
# 
# THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
# IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
# FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
# AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
# LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
# OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
# SOFTWARE.

#!/bin/bash
source $GITHUB_ACTION_PATH/lib/errmsg.sh
config=$(cat just.config.json)

docs_config=$(echo "$config" | jq -r '.docs_config')
if ! echo "$config" | jq -e '.docs_config' > /dev/null; then
    ERROR_MESSAGE=$(ErrorMessage "docs/checks.sh" "0118")
    echo -e "::error::$ERROR_MESSAGE" && exit 1
fi

validate_docs_config() {
    local metatitle=$(echo "$config" | jq -r '.docs_config.metatitle' > /dev/null)
    if [[ -z "$metatitle" ]]; then
        local ERROR_MESSAGE=$(ErrorMessage "docs/checks.sh" "0119")
        echo -e "::error::$ERROR_MESSAGE" && exit 1
    fi
    local domain=$(echo "$config" | jq -r '.docs_config.domain' > /dev/null)
    if [[ -z "$domain" ]]; then
        local ERROR_MESSAGE=$(ErrorMessage "docs/checks.sh" "0120")
        echo -e "::error::$ERROR_MESSAGE" && exit 1
    fi
}

# validate_docs_config

```
```js
/*

MIT License

Copyright (c) 2025 JustStudio. <https://juststudio.is-a.dev/>

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.

*/

const baseregex = /(@_just base)/g;
const baseregex2= /(@_just base;)/g;
const coderegex = /(@_just highlight)/g;
const coderegex2= /(@_just highlight;)/g;
const btnsregex = /(@_just buttons)/g;
const btnsregex2= /(@_just buttons;)/g;
const srchregex = /(@_just search)/g;
const srchregex2= /(@_just search;)/g;
const classRegex= /\.([a-zA-Z0-9_-]+)/g;
/**
 * @param {string} CSS 
 * @param {string?} CUSTOM
 * @param {string} CODE 
 * @param {boolean?} USECODE
 * @param {string} CSSBUTTONS
 * @param {string} CSSSEARCH
 * @returns {[string, boolean]}
 */
exports.customcss = function (CSS, CUSTOM, CODE, USECODE = true, CSSBUTTONS, CSSSEARCH) {
    const addcss = CSSBUTTONS + CSSSEARCH;
    if (!CUSTOM) {
        return [
            USECODE ? CSS + CODE + addcss : CSS + addcss,
            true,
        ];
    }
    let usedcsssearch = false;
    if (CUSTOM.replace(srchregex, CSSSEARCH).replace(srchregex2, CSSSEARCH) != CUSTOM) {
        usedcsssearch = true;
    }
    const custom = CUSTOM
        .replace(baseregex2,CSS)
        .replace(baseregex, CSS)
        .replace(coderegex2, USECODE ? CODE : '')
        .replace(coderegex, USECODE ? CODE : '')
        .replace(btnsregex, CSSBUTTONS)
        .replace(btnsregex2, CSSBUTTONS)
        .replace(srchregex, CSSSEARCH)
        .replace(srchregex2, CSSSEARCH);
    return [custom, usedcsssearch];
}

const savedclasses = {};
let language_class;
let prompt_class;
let function_class;
let classid = 0;
/**
 * @param {string} TEMPLATE 
 * @param {string} CSS 
 * @param {string} HTML 
 * @param {string} DATANAME8 
 * @returns {string[]}
 */
exports.highlightclasses = function (TEMPLATE, CSS, HTML, DATANAME8) {
    const classes = [];
    let match;
    while ((match = classRegex.exec(TEMPLATE)) !== null) {
        classes.push(match[1]);
    }
    const uniqueClasses = Array.from(new Set(classes.sort((a,b) => a.length - b.length))).filter(c => !savedclasses[c]).sort((a,b) => a.length - b.length);
    uniqueClasses.forEach(class_ => {
        savedclasses[class_] = `${DATANAME8}${classid++}`;
        if (class_ == "language_") {language_class = savedclasses[class_]}
        else if (class_ == "prompt_") {prompt_class= savedclasses[class_]}
        else if (class_=="function_"){function_class=savedclasses[class_]}
    });
    for (const [class_, hlclass] of Object.entries(savedclasses)) {
        CSS = CSS.replace(new RegExp(`.${class_}(?= |.|,)`, 'g'), `.${hlclass}`);
        const regex = new RegExp(
            `<([a-zA-Z0-9]+)([^>]*)\\sclass="([^"]*\\b${class_}\\b[^"]*)"([^>]*)>([\\s\\S]*?)</\\1>`,
            'gm'
        );
        HTML = HTML.replace(regex, (match, tagName, beforeClassAttrs, classAttrValue, afterClassAttrs, innerContent) => {
            const classes_ = classAttrValue.split(' ');
            if (!classes_.includes(hlclass)) {
                classes_.push(hlclass);
            }
            const newClassAttr = classes_.join(' ');
            return `<${tagName}${beforeClassAttrs} class="${newClassAttr.replace(/(?<=\s|^| )hljs(.*?) (?=|$)/, '').replace("language_", language_class).replace("prompt_", prompt_class).replace("function_", function_class).replace(/(.*?) \1/, '$1')}"${afterClassAttrs}>${innerContent}</${tagName}>`;
        });
        //HTML = HTML.replace(new RegExp(`<(.*?) class="(.*?)${class_}(.*?)">(.*?)</\\1>`, 'gm'), `<$1 class="$2${hlclass}$3">$4</$1>`);
    }
    return [CSS, HTML];
}

```
```js
/*

MIT License

Copyright (c) 2025 JustStudio. <https://juststudio.is-a.dev/>

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.

*/

const [light] = process.argv.slice(2);
console.log(JSON.stringify({
    "_just_light": light
}));

```
```js
/*

MIT License

Copyright (c) 2025 JustStudio. <https://juststudio.is-a.dev/>

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.

*/

const _just = {};
const [PATH] = process.argv.slice(2);
_just.string = require('../../lib/string.js');

const charset = "utf-8";
const fs = require('fs');
const path = require('path');
const rootDirA = PATH || '.';
const rootDirB = process.cwd();
try {
    const logs = fs.readFileSync(path.join(rootDirA !== '.' ? rootDirA : rootDirB, '_just_data', 'output.txt'), charset);
    let logsstr = logs;
    const outputlogs = logsstr !== '';
    const l = ['\n\n','\n    ','\n        '];
    logsstr += outputlogs? l[0] : '';
    let newlogs = `COMPRESSED:`;

    function findMarkdownFiles(dir) {
        let results = [];
        const list = fs.readdirSync(dir);
        list.forEach(file => {
            file = path.join(dir, file);
            const stat = fs.statSync(file);
            if (stat && stat.isDirectory()) {
                results = results.concat(findMarkdownFiles(file));
            } else if (file.endsWith('.md') || file.endsWith('.mdx')) {
                results.push(file);
            }
        });
        return results;
    }
    let fileID = 0;
    let toobig = false;
    findMarkdownFiles(rootDirB).forEach(file => {
        fileID++;
        if (newlogs.length >= 2 ** 28) {
            if (!toobig) {
                newlogs += '\n\n\nerror: logs are too big'
            }
            toobig = true;
            return;
        }
        newlogs += toobig ? '' : `${l[1]}FILE #${fileID} "${_just.string.removeLast(_just.string.runnerPath(file), 'md')}html":`;
        try {
            const fileNameWithoutExt = path.basename(file, path.extname(file));
            const outFilePath = (ext) => path.join(path.dirname(file), `${fileNameWithoutExt}.${ext}`);
            const htmlsize = _just.string.fileSize(fs.statSync(outFilePath('html')).size);
            newlogs += toobig ? '' : `${l[2]}SIZE: ${htmlsize} (html output)`;
        } catch (err) {
            newlogs += toobig ? '' : `${l[2]}ERROR: ${err}`;
        }
        let sl = false;
        let fd = false;
        try {
            fs.unlink(file, function(err) {
                if (!toobig) {newlogs += err ? `${l[2]}MARKDOWN FILE DELETED: NO. (${err}) (fs)` : newlogs += `${l[2]}MARKDOWN FILE DELETED: YES.`};
                sl = true;
                fd = true;
            })
        } catch (err) {
            newlogs += sl || toobig ? '' : `${l[2]}MARKDOWN FILE DELETED: NO. (${err}) (tc)`; // tc here means try{}catch(){}
            fd = true;
        }
    });

    console.log(newlogs);
    logsstr += outputlogs? newlogs : '';
    fs.writeFileSync(path.join(rootDirA !== '.' ? rootDirA : rootDirB, '_just_data', 'output.txt'), logsstr, charset);
} catch (eee) {
    // debug disabled
}
```
```js
/*

MIT License

Copyright (c) 2025 JustStudio. <https://juststudio.is-a.dev/>

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.

*/

const regex = /(?<=^|\n)_just: (prev|next): \/(.*?)(?=\n|$)/g;
exports.regex = regex;

/**
 * @param {string} text 
 * @returns {[
 *     string, 
 *     {
 *         "prev"?: string,
 *         "next"?: string
 *     }
 * ]}
 */
exports.get = (text) => {
    const data = {};
    text.replace(regex, (a, b, c) => {
        data[b] = c;
        return '';
    })
    return [
        text, 
        data
    ]
}

/**
 * 
 * @param {{
 *     "prev"?: string,
 *     "next"?: string
 * }} data 
 * @param {string} n0 
 * @param {string} n1 
 * @param {string} n2 
 * @param {string} pid 
 * @param {string} nid 
 * @param {[string[]]} pl
 * @returns {string}
 */
exports.html = (data, n0, n1, n2, pid, nid, pl) => {
    if (!data.prev && !data.next) {
        return '';
    } else {
        const dataprev = '/' + data.prev;
        const datanext = '/' + data.next;
        const pl1 = [], pl2 = {};
        for (const [id, p] of Object.entries(pl)) {
            pl1.push(p[0]);
            pl2[p[0]] = p[1];
        }
        return `<div class="${n0}"${data.next && !data.prev ? ' style="display:flex;flex-direction:column;"' :''}>${data.prev && pl1.includes(dataprev) ? `<button class="${n1}" id="${pid}"><small>Previous page</small><span>${pl2[dataprev] || data.prev}</span></button>` : ''}${data.next && pl1.includes(datanext) ? `<button class="${n2}" id="${nid}"${data.next && !data.prev ? ' style="align-self:flex-end;"' : ''}><small>Next page</small><span>${pl2[datanext] || data.next}</span></button>` : ''}</div>`;
    }
}
```
## test
```css
:root {
    --bg: #121212;
    --cl: #f0f0f0;
    --kb: #2196F3;
    --tf: 'Rubik', sans-serif;
    --bh: 32px;
    --bp: 0.5em;
    --br: 6px;
    --mp: 170px;
    --mn: 10px;
    --nh: center;
    --ft: 10px;
    --fr: 10px;
    --md: #ffffff26;
    --nt: #4964ff80;
    --nb: #4964ff2b;
    --tt: #4992ff80;
    --tb: #4992ff2b;
    --it: #6d49ff80;
    --ib: #6d49ff2b;
    --wt: #ffd84980;
    --wb: #ffd8492b;
    --ct: #ff624980;
    --cb: #ff62492b;
    --sb: 62px;
}
@media(min-width: 1250px) {
    :root {
        --mn: 50px;
        --mp: 250px;
        --nh: left;
        --ft: 0px;
        --fr: 50px;
    }
}
@media(min-width: 1500px) {
    :root {
        --mn: 200px;
        --mp: 400px;
        --fr: calc(100% + 15px);
    }
}
.l {
    --bg: #f0f0f0;
    --cl: #121212;
    --md: #00000026;
    --wt: #ffb00080;
    --wb: #ffb0002b;
}
@media (prefers-color-scheme: light) {
    .a {
        --bg: #f0f0f0;
        --cl: #121212;
        --md: #00000026;
        --wt: #ffb00080;
        --wb: #ffb0002b;
    }
}
.stb {
    --sb: 0px;
}

* {
    transition: 300ms;
    color: var(--cl);
    border-color: var(--cl);
    font-family: var(--tf);
    outline-color: transparent;
    outline-width: 3px;
    outline-offset: 3px;
    outline-style: solid;
}

html {
    width: 100%;
    overflow-wrap: break-word;
    scroll-behavior: smooth;
}
body {
    background-color: var(--bg);
    display: flex;
    margin: 0px;
    max-width: 100%;
    gap: 5px;
    flex-direction: column;
    flex-wrap: nowrap;
    align-content: center;
    justify-content: center;
    align-items: center;
}

::-webkit-scrollbar {
    width: 7px;
    height: 7px;
}
::-webkit-scrollbar-button {
    width: 0;
    height: 0
}
::-webkit-scrollbar-track {
    background: rgba(0, 0, 0, 0)
}
::-webkit-scrollbar-thumb {
    background: var(--cl);
    border: 2px solid var(--bg);
    border-radius: 10px;
}

a, button {
    cursor: pointer;
    text-decoration: underline;
    text-decoration-color: transparent;
}
button {
    text-decoration-thickness: 3px;
    opacity: 0.75;
}
a, button, input {
    font-family: var(--tf);
    border-radius: var(--br);
}
button, input {
    height: var(--bh);
    background-color: var(--cl);
    color: var(--bg);
    border: none;
    margin: 0;
    padding: 0;
    padding-left: var(--bp);
    padding-right: var(--bp);
    font-family: var(--tf);
    font-size: 14px;
    font-weight: 400;
}
.l button, .l input {
    background-color: var(--md);
    color: var(--cl);
}
@media (prefers-color-scheme: light) {
    .a button, .a input {
        background-color: var(--md);
        color: var(--cl);
    }
}
button:hover, input:hover {
    opacity: 0.85;
}
button:active {
    opacity: 0.75 !important;
    transition: 50ms;
}
a:focus-visible {
    text-decoration-color: var(--cl);
    outline-color: var(--kb);
}
button:focus-visible {
    opacity: 1;
    text-decoration-color: var(--bg);
    outline-color: var(--kb);
}
input:placeholder-shown {
    color: var(--md);
    opacity: 0.9;
}
::placeholder { /* Fix Firefox */
  color: #838383;
  opacity: 1;
}

@keyframes line {
    0%, 0.1% {
        width: 0%;
        left: 0%;
    }
    2% {
        width: 75%;
    }
    3.5%, 98% {
        width: 100%;
        left: 0%;
    }
    100% {
        width: 0%;
        left: 100%;
    }
}
@keyframes noline {
    from {
        width: 100%;
    }
    to {
        width: 0%;
    }
}

a#ext {
    padding-right: 10px;
}
a#ext:after {
    content: '\2197';
    position: fixed;
    top: 0%;
    font-family: 'Murecho', var(--tf), monospace, sans-serif;
    font-weight: 900;
    font-size: 11px;
    translate: 2px -20%;
    opacity: 0.5;
    transition: 200ms;
}
a#ext:hover:not(:focus):after {
    translate: 2px -25%;
}
.navbar {
    background-color: var(--bg);
    position: fixed;
    top: 0%;
    left: 0%;
    width: calc(100% - 30px);
    height: 50px;
    padding: 5px;
    padding-left: 15px;
    padding-right: 15px;
    display: flex;
    flex-direction: row;
    flex-wrap: nowrap;
    align-content: center;
    align-items: center;
    border-style: solid;
    border-width: 0px;
    border-bottom-width: 2px;
    gap: 15px;
    max-width: 100%;
    overflow: hidden;
    overflow-x: auto;
    justify-content: space-between;
    cursor: default;
    user-select: none;
    z-index: 2;
}
.navbar .heading {
    display: flex;
    flex-direction: row;
    flex-wrap: nowrap;
    align-content: center;
    align-items: center;
    gap: 10px;
    background-color: var(--bg);
    z-index: 1;
    filter: drop-shadow(20px 0px 5px var(--bg));
    -webkit-filter: drop-shadow(20px 0px 5px var(--bg));
}
.navbar .heading img {
    width: 35px;
}
.navbar .links {
    position: fixed;
    left: 50%;
    translate: -50%;
    display: flex;
    flex-direction: row;
    flex-wrap: nowrap;
    align-items: center;
    gap: 10px;
}
.navbar .links a {
    opacity: 0.6;
    transition: 300ms;
    filter: hue-rotate(1deg);
    -webkit-filter: hue-rotate(1deg);
}
.navbar .links a:before {
    content: '';
    position: fixed;
    width: 0%;
    height: 1px;
    left: 0%;
    top: calc(100% + 2px);
    background-color: var(--cl);
    transition: 150ms;
    z-index: -1;
    animation: 300ms noline ease-out 1;
}
.navbar .links a:hover:not(:focus) {
    opacity: 1;
    filter: drop-shadow(0px 0px 2px);
    -webkit-filter: drop-shadow(0px 0px 2px);
    transition: 150ms;
}
.navbar .links a:hover:not(:focus):before {
    width: 100%;
    left: 0%;
    animation: 6s line linear infinite;
}
.navbar .links a:focus {
    opacity: 1;
}
.navbar .buttons {
    display: flex;
    flex-direction: row;
    align-content: center;
    justify-content: flex-end;
    align-items: center;
    gap: 10px;
    position: fixed;
    right: 15px;
    z-index: 1;
}
.navbar .buttons input {
    position: absolute;
    right: calc(100% + 10px);
    display: none;
    pointer-events: none;
    user-select: none;
    z-index: -1;
}
@media(max-width: 700px) {
    .navbar {
        padding-left: 30px;
        padding-right: 30px;
        width: calc(100% - 60px);
    }
    .navbar .buttons {
        display: none;
    }
    .navbar:has(.buttons button) .links {
        left: initial;
        right: 30px;
        translate: 0%;
    }
}
@media(min-width: 1000px) {
    .navbar .buttons input {
        display: inline-block;
        pointer-events: all;
        user-select: all;
        z-index: 0;
    }
    .navbar:has(.buttons button) .links {
        left: 33%;
        translate: 0%;
    }
}
@media(min-width: 1200px) {
    .navbar:has(.buttons button) .links {
        left: 33%;
        translate: 0%;
    }
}
@media(min-width: 1600px) {
    .navbar:has(.buttons button) .links {
        left: 50%;
        translate: -50%;
    }
}
@media(max-width: 1000px) {
    .navbar:not(:has(.buttons button)) {
        padding-left: 30px;
        padding-right: 30px;
        width: calc(100% - 60px);
    }
    .navbar:not(:has(.buttons button)) .links {
        left: initial;
        right: 30px;
        translate: 0;
    }
}
@media(min-width: 1000px) {
    .navbar:not(:has(.buttons button)) .links {
        left: 50%;
        translate: -50%;
    }
}
.navbar.scroll {
    border-bottom-right-radius: var(--br);
}
body.navbar-invert .navbar.scroll {
    filter: invert();
    -webkit-filter: invert();
    border-block-width: 0px;
}
body.navbar-invert .navbar.scroll .links a:hover {
    filter: hue-rotate(1deg);
    -webkit-filter: hue-rotate(1deg);
}
body.navbar-invert:not(.navbar-noimginvert) .navbar.scroll .heading img {
    filter: invert();
    -webkit-filter: invert();
}
body.navbar-invert .navbar.scroll, .l .navbar .links a#ext:after {
    opacity: 1;
}
body.navbar-invert .navbar.scroll, .l .navbar .links a:hover {
    filter: hue-rotate(1deg);
    -webkit-filter: hue-rotate(1deg);
}
@media (prefers-color-scheme: light) {
    .a .navbar .links a#ext:after {
        opacity: 1;
    }
    .a .navbar .links a:hover {
        filter: hue-rotate(1deg);
        -webkit-filter: hue-rotate(1deg);
    }
}
.navbar .heading:not(:has(span)) {
    width: auto;
    height: auto;
    min-height: 20px;
    max-height: 35px;
}
.navbar .heading:not(:has(span)) img {
    width: auto;
    height: auto;
    max-height: 32px;
    min-height: 20px;
}
header {
    height: 62px;
    position: sticky;
    z-index: 10;
}

main {
    position: relative;
    width: 100%;
    z-index: 0;
}
main nav {
    position: sticky;
    top: 64px;
    height: 0;
    width: var(--mp);
    z-index: 3;
    padding-top: 10px;
    padding-left: 10px;
    padding-right: 10px;
    user-select: none;
}
nav.left {
    padding-left: var(--mn);
    width: calc(var(--mp) - 5px);
}
@media(max-width: 555px) {
    nav.left {
        translate: -100%;
    }
}
.navleft nav.left {
    translate: 0%;
}
nav.right {
    padding-right: var(--mn);
    left: 100%;
    z-index: 2;
}
@media (max-width: 700px) {
    nav.right {
        display: none;
    }
}
nav.right div {
    width: calc(100% - 15px - var(--mn));
    margin-top: 50px;
    translate: calc(var(--fr) + 15px);
}
nav.right div span {
    display: block;
    position: relative;
    height: 19px;
    z-index: 3;
}
nav.right div > span {
    pointer-events: none;
}
nav.right div ul {
    padding-left: 10px;
    z-index: 2;
    position: relative;
    background-color: var(--md);
}
nav.right div ul li {
    height: 20px;
    margin-top: 10px;
    background-color: var(--bg);
}
nav.right div ul li.secondary {
    translate: 10px;
    width: calc(100% - 10px);
}
nav.right div ul li:after, nav.right div ul li:before {
    content: '';
    background-color: var(--bg);
    width: 4px;
    position: fixed;
    height: 33px;
    z-index: -1;
    transition: 300ms;
}
nav.right div ul li:after {
    right: 100%;
    width: calc(100% + 4px);
    translate: calc(100% - 3.8px) calc(-100% + 1px);
}
nav.right div ul li:not(.secondary):after {
    left: 0%;
    right: initial;
    translate: 6px calc(-100% + 2px);
}
nav.right div ul li:before {
    translate: -10px -10px;
}
nav.right div ul li.secondary:before {
    height: 29px;
    translate: -26px -7px;
    width: 20px;
}
nav.right div ul li.secondary:after {
    height: 38px;
    translate: calc(100% - 3.8px) calc(-100% + 7px);
}
nav.right div ul li.secondary:has(+ .secondary):before {
    height: 40px;
}
nav.right div ul li.secondary + li:not(.secondary):after {
    height: 27px;
}
nav.right div ul span {
    padding-left: 5px;
    overflow: hidden;
}
nav.right div ul:after {
    content: '';
    position: fixed;
    width: 5px;
    height: 100%;
    top: 0%;
    left: 0%;
    background-color: var(--bg);
    translate: -1px;
    transition: 300ms;
}
nav.right div .slider {
    position: fixed;
    z-index: 1;
    top: 79px;
    height: 20px;
    width: 100%;
    left: 0%;
    background-color: var(--cl);
    translate: 0px calc(var(--hc) * 30px);
    padding: 0;
}
#main {
    position: relative;
    margin-top: -20px;
    z-index: 1;
}
.main {
    position: relative;
    top: 4px;
    padding-left: var(--mp);
    padding-right: calc(var(--mp) - 10px);
    z-index: 1;
    translate: -5px;
    padding-bottom: 50px;
}
@media(max-width: 700px) {
    .main {
        padding-right: 5px;
    }
}
@media(max-width: 555px) {
    .main {
        padding-left: 5px;
        translate: 0px;
    }
}
.navleft .main {
    padding-left: var(--mp);
    translate: -5px;
}
main nav ul:has(ul) {
    width: calc(100% - 10px - var(--mn));
    gap: 30px;
    display: flex;
    flex-direction: column;
    padding-top: 50px;
    overflow-y: auto;
    max-height: calc(100vh - 189px + var(--sb));
}
main nav ul {
    margin: 0;
    padding: 0;
}
main nav ul span strong {
    display: block;
    width: 100%;
    text-align: var(--nh);
    margin-bottom: 15px;
    pointer-events: none;
    user-select: none;
    padding-right: var(--ft);
    margin-left: calc(0px - var(--ft));
}
main nav ul a span {
    opacity: 0.75; 
}
main nav ul a span:hover, .chc span {
    opacity: 1;
}
main nav ul ul {
    display: flex;
    flex-direction: column;
    gap: 10px;
}
main nav li {
    display: block;
}
.ios .navbar .heading {
    -webkit-filter: none;
    filter: none;
}
nav.left > ul > li > span {
    margin-bottom: 15px;
}
.ios nav.right {
    display: none;
}
.ios .main {
    padding-right: 5px;
}

.main:before {
    content: '';
    position: fixed;
    top: -28px;
    width: 1px;
    height: calc(100% + 28px);
    background-color: var(--md);
    translate: -10px;
    opacity: 0.5;
}
@media(max-width: 555px) {
    .main:before {
        opacity: 0;
    }
}
.navleft .main:before {
    opacity: 0.5;
}
blockquote {
    display: block;
    margin: 0;
    margin-top: 10px;
    border-color: var(--md);
    border-style: solid;
    border-width: 0;
    border-left-width: 5px;
    padding-left: 10px;
    margin-bottom: 10px;
    margin-left: 10px;
    padding-top: 10px;
    padding-bottom: 10px;
}
blockquote blockquote {
    opacity: 0.75;
}
blockquote:hover {
    border-color: var(--cl);
    opacity: 1;
}
code {
    background-color: var(--md);
    padding-left: 5px;
    padding-right: 5px;
    border-radius: 5px;
}
code, code span {
    font-family: monospace;
    cursor: text;
}
.line {
    position: relative;
    z-index: 1;
    width: 100%;
    height: 5px;
    background-color: var(--md);
    margin-top: 20px;
    margin-bottom: 1px;
    overflow: hidden;
}
.line:after {
    content: '';
    position: relative;
    display: block;
    top: 0%;
    left: -120%;
    width: 100%;
    height: 100%;
    background-color: var(--cl);
    transition: 1s;
    transition-delay: 400ms;
    transition-timing-function: ease-in-out;
}
.line, .line:after {
    border-radius: 10px;
}
.line:hover:after {
    left: 120%;
    transition-delay: 0ms;
}
.code {
    display: block;
    width: calc(100% - 10px);
    padding: 5px;
    line-height: 18px;
    margin-top: 10px;
    margin-bottom: 10px;
}
.code:has(code) {
    margin-top: 28px;
    border-top-left-radius: 0px;
}
.code code {
    position: relative;
    display: block;
    translate: -5px -23px;
    margin-bottom: -20px;
    width: max-content;
    border-bottom-left-radius: 0px;
    border-bottom-right-radius: 0px;
    pointer-events: none;
    white-space: nowrap;
    user-select: none;
}
.note, .ntip, .impr, .warn, .caut {
    border-top-right-radius: 10px;
    border-bottom-right-radius: 10px;
    padding-top: 35px;
    line-height: 20px;
    padding-right: 10px;
}
.note:before, .ntip:before, .impr:before, .warn:before, .caut:before {
    position: fixed;
    translate: 0px -25px;
    font-weight: 500;
    height: 20px;
}
.note {
    background-color: var(--nb);
    border-color: var(--nt) !important;
}
.note:before {
    content: 'Note';
    color: var(--nt);
}
.ntip {
    background-color: var(--tb);
    border-color: var(--tt) !important;
}
.ntip:before {
    content: 'Tip';
    color: var(--tt);
}
.impr {
    background-color: var(--ib);
    border-color: var(--it) !important;
}
.impr:before {
    content: 'Important';
    color: var(--it);
}
.warn {
    background-color: var(--wb);
    border-color: var(--wt) !important;
}
.warn:before {
    content: 'Warning';
    color: var(--wt);
}
.caut {
    background-color: var(--cb);
    border-color: var(--ct) !important;
}
.caut:before {
    content: 'Caution';
    color: var(--ct);
}

footer {
    position: relative;
    background-color: var(--bg);
    z-index: 2;
    padding: 20px;
    display: flex;
    flex-direction: row;
    flex-wrap: nowrap;
    align-content: center;
    justify-content: space-between;
    align-items: center;
}
footer * {
    height: 20px;
}
footer div {
    border-radius: 10px;
    overflow: hidden;
    display: flex;
    gap: 5px;   
}
footer div button {
    aspect-ratio: 1;
    border-radius: 50%;
    background-color: var(--md);
    padding: 0;
    width: 20px;
    max-width: 20px;
    color: var(--cl);
    font-family: var(--tf);
    font-weight: 600;
    overflow: hidden;
}
footer div button svg {
    position: relative;
    width: 20px;
    top: 50%;
    left: 50%;
    translate: -50% -50%;
    -webkit-translate: -50% -50%;
    scale: 0.75;
    opacity: 0.75;
    fill: var(--cl);
    transition: 700ms;
}
footer div button:hover svg {
    transform: rotate(10deg);
    -webkit-transform: rotate(10deg);
}
footer div button:focus-visible {
    outline-offset: 0;
    background-color: var(--kb);
}
footer div:has(button:focus-visible) {
    outline-style: solid;
    outline-width: 5px;
    outline-offset: 5px;
    outline-color: var(--cl);
}
footer div, footer button {
    border: 1px solid var(--md);
}
footer:before {
    content: '';
    position: absolute;
    bottom: 59px;
    right: 0px;
    width: 100%;
    height: 1px;
    background-color: var(--md);
    opacity: 0.5;
}
html.l:not(.a) #l, html:not(.l):not(.a) #d, html.a #a{
    border-color: var(--cl)
}
#l svg circle {
    fill: var(--cl);
    scale: 0.9;
    translate: 5% 5%;
}
#d {
    transform: rotate(5deg);
    -webkit-transform: rotate(5deg);
}
#d svg {
    scale: 0.7;
}
#a {
    text-align: center;
    vertical-align: middle;
    align-content: center;
    align-items: center;
}
.ios footer div:has(button) {
    display: none;
}
#main small {
    display: none;
    opacity: 0.25;
    width: 100%;
    text-align: center;
}
@media(max-width: 555px) {
    #main small {
        display: block;
    }
}
.navleft #main small {
    display: none;
}


h1 {
    display: block;
    font-size: 2em;
    margin-block-start: 0.67em;
    margin-block-end: 0.67em;
    margin-inline-start: 0px;
    margin-inline-end: 0px;
    font-weight: bold;
    unicode-bidi: isolate;
}
h2 {
    display: block;
    font-size: 1.5em;
    margin-block-start: 0.83em;
    margin-block-end: 0.83em;
    margin-inline-start: 0px;
    margin-inline-end: 0px;
    font-weight: bold;
    unicode-bidi: isolate;
}
h3 {
    display: block;
    font-size: 1.17em;
    margin-block-start: 1em;
    margin-block-end: 1em;
    margin-inline-start: 0px;
    margin-inline-end: 0px;
    font-weight: bold;
    unicode-bidi: isolate;
}
h4 {
    display: block;
    margin-block-start: 1.33em;
    margin-block-end: 1.33em;
    margin-inline-start: 0px;
    margin-inline-end: 0px;
    font-weight: bold;
    unicode-bidi: isolate;
}
h5 {
    display: block;
    font-size: 0.83em;
    margin-block-start: 1.67em;
    margin-block-end: 1.67em;
    margin-inline-start: 0px;
    margin-inline-end: 0px;
    font-weight: bold;
    unicode-bidi: isolate;
}
h6 {
    display: block;
    font-size: 0.67em;
    margin-block-start: 2.33em;
    margin-block-end: 2.33em;
    margin-inline-start: 0px;
    margin-inline-end: 0px;
    font-weight: bold;
    unicode-bidi: isolate;
}
p {
    display: block;
    margin-block-start: 0.5em;
    margin-block-end: 0.5em;
    margin-inline-start: 0px;
    margin-inline-end: 0px;
    unicode-bidi: isolate;
}

.debug * {
    outline-color: #888888;
    outline-offset: 0;
    outline-width: 1px;
    outline-style: dashed;
}
.debug .main {
    z-index: 2;
}
.debug .main span {
    background-color: blue;
    width: 100%;
    display: block;
}
.debug .left, .debug .right {
    background-color: #ff000080;
}
.debug .left ul, .debug .right div {
    background-color: red;
}
.debug .navbar {
    background-color: green;
}
.debug nav.right div *:not(.slider), .debug nav.right div *:after, .debug nav.right div *:before {
    background-color: transparent;
    color: black;
}

h1, h2, h3 {
    padding-top: 62px; margin-top: -62px;
}
.underline, .main a {
    text-decoration: underline;
}
.main a {
    color: var(--kb);
}

.search {
    position: fixed;
    padding-top: 16px;
    padding-left: 7px;
    padding-right: 7px;
    padding-bottom: 6px;
    translate: 0px -6px;
    display: flex;
    flex-direction: column;
    flex-wrap: nowrap;
    align-content: flex-start;
    align-items: flex-start;
    justify-content: flex-start;
    gap: 10px;
    background-color: var(--md);
    backdrop-filter: blur(4px) url("#glass") brightness(0.3);
    -webkit-backdrop-filter: blur(4px) url("#glass") brightness(0.3);
    z-index: -1;
    border-bottom-left-radius: 10px;
    border-bottom-right-radius: 10px;
    overflow: hidden;
    border: 1px solid var(--md);
}
.search a {
    height: 19px;
    overflow: hidden;
}
.search span {
    opacity: 0.75;
    width: 100%;
    text-align: center;
}
.firefox .search, .ios .search {
    backdrop-filter: blur(4px) brightness(0.3) !important;
    -webkit-backdrop-filter: blur(4px) brightness(0.3) !important;
}
.ios .search {
    background-color: var(--bg);
}

.line-through {
    text-decoration: line-through;
}
mark {
    background-color: var(--wt);
    padding-left: 2px;
    padding-right: 2px;
    border-radius: 5px;
}

.main a#ext:after {
    position: relative !important;
    display: inline-block;
}
.main img {
    max-width: 100%;
    border-radius: 5px;
}

input[type="checkbox"] {
    pointer-events: none;
    accent-color: var(--kb) !important;
    height: auto;
    display: inline;
}

.code:has(code) span:has(div) {
    padding-left: 15px;
}
.code:has(code) span div {
    display: inline;
    position: absolute;
    width: 10px;
    height: 10px;
    translate: -12.5px 3.5px;
    border-radius: 50%;
}
.code, .code code, .code * {
    background: #3c3c3c;
    color: #ffffff;
}
.l .code, .l .code code, .l .code * {
    background: #e5e5e5;
    color: #000000;
}
@media (prefers-color-scheme: light) {
    .a .code, .a .code code, .a .code * {
        background: #e5e5e5;
        color: #000000;
    }
}

html:before, html:after, body:before, body:after {
    content: none !important;
}
header, main {
    display: block !important;
}

.error:before {
    content: 'Uh oh!' !important;
    font-size: 2em;
    position: fixed;
    top: 50%;
    translate: 0 calc(-100% - 6px);
    width: 50%;
    text-align: center;
    border-bottom: 1px solid white;
    white-space: nowrap;
}
.error:after {
    content: 'Something went wrong.' !important;
    position: fixed;
    top: 50%;
}
html:has(.error):before {
    content: '_just' !important;
    font-size: 13px;
    position: fixed;
    bottom: 5px;
    left: 5px;
    line-break: anywhere;
    margin-right: 5px;
}
html:has(.error):after {
    content: var(--edata) !important;
    position: fixed;
    top: calc(50% + 24px);
    width: 100%;
    text-align: center;
    font-size: 12px;
    opacity: 0.5;
}
.error * {
    display: none !important;
}

#search {
    display: none;
}

.small {
    opacity: 0.5;
    font-size: 12px;
}

html:has(.navleft) {
    overflow: hidden;
    touch-action: pan-y;
}
html:has(.navleft) .main {
    pointer-events: none;
    user-select: none;
    filter: blur(3px) brightness(0.8);
    -webkit-filter: blur(3px) brightness(0.8);
}
.navleft .navbar .links {
    translate: calc(0px - calc(var(--mp) - 10px)) !important;
}

.main .linkspace {
    margin-right: -12px;
}
.main .linkspace:after {
    translate: 0px -20%;
}
.main .linkspace:hover:not(:focus):after {
    translate: 0px -25%;
}

.main .linkmark {
    margin-right: -17px;
}
.main .linkmark:after {
    translate: -1px -70%;
}
.main .linkmark:hover:not(:focus) {
    margin-right: -10px;
}
.main .linkmark:hover:not(:focus):after {
    translate: 0px -25%;
}

.main #ext {
    white-space: nowrap;
}

.main .linkdot {
    margin-right: -17px;
}
.main .linkdot:after {
    translate: 2px -40%;
}
.main .linkdot:hover:not(:focus):after {
    translate: 2px -45%;
}

main nav li ul {
    padding-top: 0px;
    width: 100%;
}
main nav li ul li:has(strong) {
    padding-top: 10px;
}
@media(min-width: 1250px) {
    main nav li ul li:has(strong) {
        padding-left: 10px;
    }
    main nav li ul li:has(strong):before {
        content: '';
        height: var(--liheight);
        width: 3px;
        background-color: var(--md);
        display: block;
        position: relative;
        translate: -10px 0px;
        border-radius: 50px;
        opacity: 0.5;
        margin-bottom: calc(0px - var(--liheight));
        transition: 300ms;
    }
}

nav.left:not(:has(strong)) > ul {
    padding-top: 50px;
    display: flex;
    flex-direction: column;
    gap: 10px;
}

#contents {
    overflow-y: scroll;
    height: calc(var(--contents) - 50px);
    overflow-x: clip;
}

input[type="checkbox"] {
    height: 13px;
}

main nav.left li {
    overflow-y: hidden;
}

.copycode {
    position: fixed;
    translate: calc(var(--codewidth) - 26px) calc(calc(0px - var(--codeheight)) + 4px);
    display: inline-block;
    height: 25px;
    border-radius: 10px;
    overflow: hidden;
}
.copycode svg {
    padding: 5px;
}

@keyframes s-shake {
    10%, 90% {
        transform: translateX(-0.5px)
    }

    20%, 80% {
        transform: translateX(1px)
    }

    30%, 50%, 70% {
        transform: translateX(-2px)
    }

    40%, 60% {
        transform: translateX(2px)
    }
}

.s-shake {
    animation: s-shake 0.5s cubic-bezier(0.68, -0.55, 0.27, 1.55) both
}

```
```css
#search {
    background-color: var(--bg);
    border: 2px solid var(--cl);
    font-family: Monospace;
    position: fixed;
    z-index: 23;
    display: block !important;
    width: 20px;
    height: 16px;
    padding-bottom: 1px;
    translate: calc(-100% - 5px) -50%;
    text-align: center;
    border-radius: 50px;
    cursor: pointer;
    user-select: none;
    transition: none;
}
.ios #search {
    display: none !important;
}

.next {
    padding: 10px;
    margin-top: 45px;
    border: 2px solid var(--md);
    border-radius: 20px;
    max-width: 769px;
    position: relative;
    translate: -50% 0px;
    left: 50%;
}
.next:before {
    content: '';
    background-color: var(--md);
    opacity: 0.5;
    width: calc(100% + 20px);
    height: 1px;
    display: block;
    top: -30px;
    position: relative;
    left: -10px;
}
.next button {
    width: 50%;
    background-color: transparent;
    color: var(--cl);
    font-weight: bold;
    opacity: 1;
    border-radius: 20px;
    overflow: hidden;
}
.next .next1 {
    border-right: 1px solid var(--md);
    border-top-right-radius: 0px;
    border-bottom-right-radius: 0px;
}
.next .next2 {
    border-left: 1px solid var(--md);
    border-top-left-radius: 0px;
    border-bottom-left-radius: 0px;
}
.next button:hover {
    scale: 101%;
    background-color: #ffffff20;
}
.l .next button:hover {
    background-color: #00000020;
}
@media (prefers-color-scheme: light) {
    .a .next button:hover {
        background-color: #00000020;
    }
}
.next button small, .next button span {
    display: block !important;
    white-space: nowrap;
}
.next button small {
    font-weight: normal;
}
.next button:hover small, .next button:hover span {
    translate: 0px -50%;
}
.next button:hover small {
    scale: 103%;
    opacity: 0 !important;
    filter: blur(2px);
    -webkit-filter: blur(2px);
}
@media (max-width: 450px) {
    .next {
        display: none;
    }
}

.code {
    cursor: default;
    white-space: nowrap;
    overflow-x: scroll;
}
.code::-webkit-scrollbar {
    width: 5px;
    height: 5px
}
.code::-webkit-scrollbar-thumb {
    border: 1px solid var(--bg);
}
.code code {
    position: absolute;
}

```
## test
```css
.hljs-number, .hljs-bullet {
    color: #eda31b;
}
.hljs-meta, .hljs-variable.language_, .language_, .hljs-params, .hljs-subst, .hljs-string .hljs-variable, .hljs-quote {
    color: #d0d1ff;
}
.hljs-meta.prompt_, .hljs-selector-id, .prompt_, .hljs-doctag, .hljs-link {
    color: #29d3a9;
}
.hljs-comment, .hljs-tag, .hljs-code {
    color: #a2a2a2;
}
.hljs-keyword, .hljs-attr, .hljs-selector-class, .hljs-symbol {
    color: #c3acff;
}
.hljs-name, .hljs-selector-tag, .hljs-title.function_, .function_, .hljs-property, .hljs-section, .hljs-strong, .hljs-emphasis {
    color: #89a8f9;
}
.hljs-string, .hljs-type {
    color: #afffaf;
}
.hljs-literal, .hljs-selector-pseudo, .hljs-function, .hljs-function .hljs-title {
    color: #faadf9;
}
.hljs-built_in, .hljs-attribute, .hljs-regexp {
    color: #aad7ff;
}

.hljs-strong {
    font-weight: bolder;
}
.hljs-emphasis {
    font-style: italic;
}

.hljs-addition, .hljs-deletion {
    padding-left: 3px;
    padding-right: 3px;
    margin-left: -3px;
    margin-right: -3px;
    border-radius: 5px
}
.hljs-addition {
    background-color: #2a8c2e80;
}
.hljs-deletion {
    background-color: #8c2a2a80;
}

```
```css
.hljs-number, .hljs-bullet {
    color: #eda31b;
}
.hljs-meta, .hljs-variable.language_, .language_, .hljs-params, .hljs-subst, .hljs-string .hljs-variable, .hljs-quote {
    color: #4c50ff;
}
.hljs-meta.prompt_, .hljs-selector-id, .prompt_, .hljs-doctag, .hljs-link {
    color: #05975e;
}
.hljs-comment, .hljs-tag, .hljs-code {
    color: #a2a2a2;
}
.hljs-keyword, .hljs-attr, .hljs-selector-class, .hljs-symbol {
    color: #723cff;
}
.hljs-name, .hljs-selector-tag, .hljs-title.function_, .function_, .hljs-property, .hljs-section, .hljs-strong, .hljs-emphasis {
    color: #1e5cff;
}
.hljs-string, .hljs-type {
    color: #00aa00;
}
.hljs-literal, .hljs-selector-pseudo, .hljs-function, .hljs-function .hljs-title {
    color: #d745d5;
}
.hljs-built_in, .hljs-attribute, .hljs-regexp {
    color: #2f9dff;
}

.hljs-strong {
    font-weight: bolder;
}
.hljs-emphasis {
    font-style: italic;
}

.hljs-addition, .hljs-deletion {
    padding-left: 3px;
    padding-right: 3px;
    margin-left: -3px;
    margin-right: -3px;
    border-radius: 5px
}
.hljs-addition {
    background-color: #95ec9980;
}
.hljs-deletion {
    background-color: #e1868680;
}

```
```js
(async()=>{
    const fcrt_ = []["filter"]["constructor"]("return globalThis")() || []["filter"]["constructor"]("return this")();
    const wndw_ = fcrt_;
    const dcmnt = fcrt_["document"];
    const theme = localStorage.getItem('t');

    const navbar = dcmnt.querySelector('[data-just="navbar"]');
    const style = document.createElement('style');
    const css = await fetch('/_just/REPLACE_CSS.css').then(r => r.text());
    style.innerHTML = css + '@media(min-width:700px){[data-just="navbar"] div:has(a){left:50% !important;translate:-50% !important}}';
    dcmnt.head.appendChild(style);
    navbar.innerHTML = 'REPLACE_NAVBAR';

    'REPLACE_THEME';

    'REPLACE_BUTTONS';
})();

```
```html
<!DOCTYPE html>
<html>
    <head>
        <meta charset="REPLACE_CHARSET">
        <meta name="viewport" content="REPLACE_VIEWPORT">
        <title>REPLACE_TITLE</title>
        <link rel="preload" href="/_just/REPLACE_CSS.css" as="style">
        <link rel="preload" href="/_just/REPLACE_JS.js" as="script">
        <link href="/_just/REPLACE_CSS.css" rel="stylesheet">
        <script src="/_just/REPLACE_JS.js"></script>
        REPLACE_DATA
        <link rel="preconnect" href="https://fonts.googleapis.com">
        <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
        <link rel="preload" href="https://fonts.googleapis.com/css2?family=Murecho:wght@100..900&family=Rubik:ital,wght@0,300..900;1,300..900&display=swap" as="style">
        <link href="https://fonts.googleapis.com/css2?family=Murecho:wght@100..900&family=Rubik:ital,wght@0,300..900;1,300..900&display=swap" rel="stylesheet">
        REPLACE_CUSTOM
        <noscript>
            <style>
                html > body > header > nav:first-of-type > div:last-of-type, 
                html > body > main > div:first-of-type > nav:last-of-type > div > div, 
                html > body > main > footer > div {
                    display: none;
                }
                html > body > header > nav:first-of-type > :nth-child(2):not(a) {
                    left: 100% !important;
                    translate: calc(-15px - 100%) !important;
                    width: max-content;
                    @media(max-width: 700px) {
                        translate: calc(-30px - 100%) !important;
                    }
                }
                html > body > main > div > small {
                    display: none !important;
                }
            </style>
        </noscript>
        <style>
            html:before {
                content: '_just';
                font-size: 13px;
                position: fixed;
                bottom: 5px;
                left: 5px;
                line-break: anywhere;
                margin-right: 5px;
            }
            html:after {
                content: 'Couldn’t load the website. (0302)';
                position: fixed;
                top: calc(50% + 24px);
                width: 100%;
                text-align: center;
                font-size: 12px;
                opacity: 0.5;
            }
            body:before {
                content: 'Uh oh!';
                font-size: 2em;
                position: fixed;
                top: 50%;
                translate: 0 calc(-100% - 6px);
                width: 100%;
                text-align: center;
                border-bottom: 1px solid white;
                white-space: nowrap;
            }
            body:after {
                content: 'Something went wrong.';
                position: fixed;
                top: 50%;
                width: 100%;
                text-align: center;
            }
            html {
                color: white;
                background-color: black;
            }
            header, main {
                display: none;
            }
        </style>
    </head>
    <body style="--hc: 0"><script>0</script>
        <header>
            <nav class="navbar">
                <div class="heading">
                    REPLACE_LOGO
                    REPLACE_NAME
                </div>
                <div class="links">
                    REPLACE_LINKS
                </div>
                <div class="buttons">
                    REPLACE_BUTTONS
                    <input placeholder="Search documentation" id="searchbar" disabled>
                    <span id="search">REPLACE_SEARCHKEY</span>
                    <div class="search"></div>
                </div>
            </nav>
        </header>
        <main>
            <div id="main">
                <nav class="left">
                    <ul>
                        REPLACE_PAGES
                    </ul>
                </nav>
                <nav class="right">
                    REPLACE_CONTENTS
                </nav>
                <article class="main">
                    REPLACE_CONTENT
                    REPLACE_PREVNEXT
                </article>
                <small>Swipe right to open the menu and swipe left to close it.</small>
            </div>
            <footer>
                <div>
                    <button id="l" title="Switch to Light Theme" type="button">
                        <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" alt="Switch to Light Theme">
                            <circle cx="12" cy="12" r="5"></circle>
                            <line x1="12" y1="1" x2="12" y2="4"></line>
                            <line x1="12" y1="20" x2="12" y2="23"></line>
                            <line x1="4.22" y1="4.22" x2="6.34" y2="6.34"></line>
                            <line x1="17.66" y1="17.66" x2="19.78" y2="19.78"></line>
                            <line x1="1" y1="12" x2="4" y2="12"></line>
                            <line x1="20" y1="12" x2="23" y2="12"></line>
                            <line x1="4.22" y1="19.78" x2="6.34" y2="17.66"></line>
                            <line x1="17.66" y1="6.34" x2="19.78" y2="4.22"></line>
                        </svg>
                    </button>
                    <button id="d" title="Switch to Dark Theme" type="button">
                        <svg xmlns="http://www.w3.org/2000/svg" height="20px" viewBox="0 -960 960 960" width="20px" alt="Switch to Dark Theme">
                            <path d="M480-120q-151 0-255.5-104.5T120-480q0-138 90-239.5T440-838q13-2 23 3.5t16 14.5q6 9 6.5 21t-7.5 23q-17 26-25.5 55t-8.5 61q0 90 63 153t153 63q31 0 61.5-9t54.5-25q11-7 22.5-6.5T819-479q10 5 15.5 15t3.5 24q-14 138-117.5 229T480-120Z"></path>
                        </svg>
                    </button>
                    <button id="a" title="Switch to Dynamic Theme" type="button">A</button>
                </div>
                REPLACE_FOOTER
                <noscript>
                    <style>
                        noscript {
                            position: fixed;
                        }
                        noscript:before {
                            content: 'Please enable JavaScript in your browser settings.';
                            position: fixed;
                            left: 0px;
                            translate: 0px 62px;
                            font-weight: bolder;
                            font-size: 2em;
                            width: 100%;
                            text-align: center;
                            height: 100%;
                            background-color: var(--cb);
                            outline: 20px solid var(--ct);
                        }
                    </style>
                </noscript>
            </footer>
        </main>
        <svg xmlns="http://www.w3.org/2000/svg" width="0" height="0" style="position:absolute;overflow:hidden;">
            <defs>
                <filter id="glass" x="0%" y="0%" width="100%" height="100%">
                    <feTurbulence type="fractalNoise" baseFrequency="0.008 0.008" numOctaves="2" seed="92" result="noise"></feTurbulence>
                    <feGaussianBlur in="noise" stdDeviation="20" result="blurred"></feGaussianBlur>
                    <feDisplacementMap in="SourceGraphic" in2="blurred" scale="30" xChannelSelector="R" yChannelSelector="G"></feDisplacementMap>
                </filter>
            </defs>
        </svg>
        <script>REPLACE_SCRIPT</script>
    </body>
</html>
```
```js
const fcrt_ = []["filter"]["constructor"]("return globalThis")() || []["filter"]["constructor"]("return this")();
const wndw_ = fcrt_;
const dcmnt = fcrt_["document"];
const page_ = 'p' + wndw_.location.pathname;
const scrll = localStorage.getItem('s' + page_);
const theme = localStorage.getItem('t');
const main_ = 'html > body > main > div#main > article.main';
const IsIOS=()=>{
    return (/iPad|iPhone|iPod/.test(wndw_.navigator.userAgent) && !wndw_.MSStream) || (/Mac/.test(wndw_.navigator.userAgent) && wndw_.innerWidth <= 700);
};
const ISIOS=IsIOS();
const isIOS=()=>ISIOS;

const SETTINGS = {
    "publicOutput": 'REPLACE_PUBLICOUTPUT',
    "searchV2": 'REPLACE_SEARCHV2',
    "output": 'REPLACE_OUTPUT'
};
if (SETTINGS.output) {
    console.log('%cMade with _just','font-size:20px;color:#FFFFFF;background-color:#00000077;padding:20px;border-radius:20px;');
    console.log('%chttps://just.is-a.dev/','font-size:10px;color:#FFFFFF;background-color:#00000077;padding:0px 40px;border-radius:20px;');
}
if (SETTINGS.publicOutput) {
    console.log(`_just output: ${wndw_.location.protocol}//${wndw_.location.hostname}/_just_data/output.txt`)
};

const throwError = (code) => {
    const messages = {
        '0301': 'Wayback Machine detected.'
    };
    dcmnt.body.classList.add('error');
    dcmnt.documentElement.style.setProperty('--edata', `'${messages[code] || 'Just an Ultimate Site Tool: A client-side error has occurred.'} (${code})'`);
    throw new Error(`REPLACE_ERRORPREFIX ${code}. For more information, visit https://just.is-a.dev/errors/${code}`);
};

const checkElement = (elements) => {
    elements.forEach(elem => {
        if (elem === null) {
            throwError('0302');
        }
    });
};

const convertbase =(str,fromBase,toBase,DIGITS="0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ+/")=>{
    const cbadd = (x, y, base) => {
        let z = [];
        const n = Math.max(x.length, y.length);
        let carry = 0;
        let i = 0;
        while (i < n || carry) {
            const xi = i < x.length ? x[i] : 0;
            const yi = i < y.length ? y[i] : 0;
            const zi = carry + xi + yi;
            z.push(zi % base);
            carry = Math.floor(zi / base);
            i++;
        }
        return z;
    };

    const multiplyByNumber = (num, x, base) => {
        if (num < 0) return(null);
        if (num == 0) return [];

        let result = [];
        let power = x;
        while (true) {
            num & 1 && (result = cbadd(result, power, base));
            num = num >> 1;
            if (num === 0) break;
            power = cbadd(power, power, base);
        }

        return result;
    };

    const parseToDigitsArray = (str) => {
        const digits = str.split('');
        let arr = [];
        for (let i = digits.length - 1; i >= 0; i--) {
            const n = DIGITS.indexOf(digits[i]);
            if (n == -1) return(null);
            arr.push(n);
        }
        return arr;
    };

    const digits = parseToDigitsArray(str);
    if (digits === (null)) return(null);

    let outArray = [];
    let power = [1];
    for (let i = 0; i < digits.length; i++) {
        digits[i] && (outArray = cbadd(outArray, multiplyByNumber(digits[i], power, toBase), toBase));
        power = multiplyByNumber(fromBase, power, toBase);
    };

    let out = '';
    for (let i = outArray.length - 1; i >= 0; i--){
        out += DIGITS[outArray[i]]};

    return out;
};
let stb_ = false;
const updateNavRight = () => {
    const navright = dcmnt.getElementById('contents');
    const offset = stb_ ? -136 : -84;
    navright.style.setProperty('--contents', `${wndw_.innerHeight + offset}px`);
};
let lhc = 0;
wndw_.addEventListener('scroll', () => {
    let headerIndex_ = false;
    checkElement([dcmnt.querySelector(".navbar")]);

    const scrollPosition = wndw_.scrollY || dcmnt.documentElement.scrollTop;

    if (scrollPosition > 150) {
        dcmnt.querySelector(".navbar").classList.add("scroll");
    } else {
        headerIndex_ = true;
        dcmnt.querySelector(".navbar").classList.remove("scroll");
    };

    localStorage.setItem('s' + page_, convertbase(scrollPosition.toString(10), 10, 64));

    const elements = dcmnt.querySelectorAll(`${main_} h1, ${main_} h2, ${main_} h3, ${main_} h4`);
    let headerIndex = -1;
    let headers;
    let lastindex = undefined;

    elements.forEach((element, index_) => {
        const rect = element.getBoundingClientRect();
        const isInView = (rect.top + rect.height / 2) <= (wndw_.innerHeight / 2);

        if (lastindex === undefined) {
            lastindex = index_;
        } else if (index_ > lastindex) {
            lastindex = index_;
            headers = index_;
        }

        if (isInView) {
            headerIndex = index_;
        }
    });

    const { scrollHeight, scrollTop, clientHeight } = dcmnt.documentElement;
    if (scrollTop + clientHeight >= scrollHeight) {
        dcmnt.body.classList.add('stb');
        stb_ = true;
        headerIndex = headers;
    } else {
        dcmnt.body.classList.remove('stb');
        stb_ = false;
    };

    const hc_ = headerIndex_ ? 0 : headerIndex >= 0 ? headerIndex : 0;
    const nr = 'REPLACE_NR';
    const _hc = 'REPLACE_CHC';
    dcmnt.body.style.setProperty('--hc', hc_);
    try {
        dcmnt.getElementById(`${nr}${lhc}`).classList.remove(_hc);
        dcmnt.getElementById(`${nr}${hc_}`).classList.add(_hc);
        lhc = hc_;
    } catch (__e) {}
    updateNavRight();
});

if (scrll) {
    dcmnt.documentElement.scrollTo(0, convertbase(scrll,64,10));
}

let swipe;
let navv = false;
const handleSwipeLeft=()=>{
    dcmnt.body.classList.remove('navleft');
    navv = false;
};
const handleSwipeRight=()=>{
    dcmnt.body.classList.add('navleft');
    navv = true;
};
dcmnt.addEventListener('touchstart', function(event) {
    swipe = [event.touches[0].clientX, event.touches[0].clientY];
}, false);
dcmnt.addEventListener('touchend', function(event) {
    const endX = event.changedTouches[0].clientX;
    const endY = event.changedTouches[0].clientY;
    const distanceX = endX - swipe[0];
    const distanceY = endY - swipe[1];

    if (distanceY < 35 && distanceY > -35) {
        if (distanceX > 35) {
            handleSwipeRight();
        } else if (distanceX < -35) {
            handleSwipeLeft();
        }
    }
}, false);

'REPLACE_THEME';

const updateMinHeight = () => {
    try {
        dcmnt.querySelector('.main').style.minHeight = `${wndw_.innerHeight-62*2-1}px`
    } catch (err_) {}
};
const updateWidth = () => {
    if (wndw_.innerWidth < 556) {
        try {
            dcmnt.querySelector('.main').style.width =(null);
            dcmnt.querySelector('.main').style.width = `${dcmnt.querySelector('.main').offsetWidth - 10}px`
        } catch (err_) {}
    } else {
        try {
            dcmnt.querySelector('.main').style.width =(null);
        } catch (err_) {}
    }
};
updateMinHeight();updateWidth();
wndw_.addEventListener('resize', ()=>{
    updateMinHeight();
    if (navv) {
        handleSwipeLeft();
    }
    updateWidth();
    updateNavRight();
});

let fun_function = false;
const glass = 'url("#glass")';
const i_want_liquid_glass = () => {
    dcmnt.body.style.filter = glass;
    dcmnt.body.style.webkitFilter = glass;
    if (fun_function) {
        dcmnt.querySelector('feDisplacementMap').scale.baseVal += 100;
    };
    fun_function = true;
};

const search1 = (data, searchTerm) => {
  const lowerSearchTerm = searchTerm.toLowerCase();

  for (const key in data) {
    if (data.hasOwnProperty(key)) {
      const value_ = data[key];
      const lowerValue = value_.toLowerCase();
      const index = lowerValue.indexOf(lowerSearchTerm);
      
      if (index !== -1) {
        const start = Math.max(0, index - 6);
        let end = SETTINGS.searchV2 ? value_.length : Math.min(value_.length, index + searchTerm.length + 9);
        
        let snippet = value_.substring(start, end);
        
        const regex = new RegExp(`(?<=\s|^|[.,!?;: \n])(${searchTerm})(?=\s|[.,!?;: \n]|$)`, 'gi');
        
        snippet = snippet.replace(regex, '<strong>$1</strong>');
        if (start > 0) {snippet = '...'+snippet.slice(3)}
        if (end < value_.length) {snippet = snippet.trim()+'...'};
        
        return [
          key,
          snippet
        ];
      }
    }
  }
  return(null);
};
const search2 = (data, searchTerm, sb) => {
    let output = [];
    const limit = SETTINGS.searchV2 ? Math.floor((wndw_.innerHeight-(sb.offsetTop+sb.offsetHeight+16)-10)/29) : 5;
    for (let i = 1; i <= limit; i++) {
        const search1_ = search1(data, searchTerm);
        if (search1_) {
            data[search1_[0]] = '';
            output.push(search1_);
        }
    }
    return output;
};

let cooldown0 = false;
const cooldown = (timems, cdvarid) => {
    switch(cdvarid) {
        case 0:    
            cooldown0=true;
            setTimeout(()=>{cooldown0=false;},timems);
        default:
            return true;
    }
};

let searchurl = "/_just/search";
dcmnt.addEventListener('DOMContentLoaded', () => {
    checkElement([dcmnt.querySelector('.main')]);

    let ltb = dcmnt.getElementById('l');
    let dtb = dcmnt.getElementById('d');
    let atb = dcmnt.getElementById('a');

    const iosautotheme = () => {
        if (isIOS()) {
            dcmnt.body.classList.add('ios');
            dcmnt.documentElement.classList.add('a');
            localStorage.setItem('t', 'a');
            autotheme();
            return true;
        } else {
            return false;
        };
    };

    if (ltb && dtb && atb) {
        ltb.addEventListener('click', () => {
            if (!iosautotheme()) {
                dcmnt.documentElement.classList.add('l');
                dcmnt.documentElement.classList.remove('a');
                localStorage.setItem('t', 'l');
            }
        });

        dtb.addEventListener('click', () => {
            if (!iosautotheme()) {
                dcmnt.documentElement.classList.remove('l');
                dcmnt.documentElement.classList.remove('a');
                localStorage.setItem('t', 'd');
            }
        });

        atb.addEventListener('click', () => {
            if (!iosautotheme()) {
                dcmnt.documentElement.classList.add('a');
                localStorage.setItem('t', 'a');
                autotheme();
            }
        });
    }

    iosautotheme();
    if (wndw_.navigator.userAgent.toLowerCase().includes('firefox')) {
        dcmnt.body.classList.add('firefox');
    };
    const wm = dcmnt.getElementById('wm-ipp-base');
    if(wm){wm.parentElement.removeChild(wm);}
    if((wndw_.location.hostname==='web.archive.org'||wm)&&'REPLACE_NOWEBARCHIVE'){
        throwError('0301');
    }

    const sb = dcmnt.getElementById("searchbar");
    sb.style.cursor = 'text';
    sb.disabled = false;
    const sd = dcmnt.querySelector('.search');
    const sk = dcmnt.getElementById("search");
    checkElement([sb, sd, sk]);
    sk.style.cursor = 'pointer';
    const updateSD = (toggle = false) => {
        let run = true;
        if (cooldown0) run = false; else {
            cooldown(300,0)
        };
        if (!toggle && run) {sd.innerHTML = ''};
        const leftt = sb.offsetLeft + sb.parentElement.offsetLeft;
        const toppp = sb.parentElement.offsetTop + sb.offsetHeight - (sb.parentElement.offsetWidth == 0 ? 15 : 0);
        sd.style.left = run ? `${leftt}px` : sd.style.left;
        sd.style.top = run ? `${toppp}px` : sd.style.top;
        sd.style.width = run ? `${sb.offsetWidth - 8*2}px` : sd.style.width;
        if (run) {
            sd.style.opacity = toggle ? 1 : 0;
            sd.style.pointerEvents = toggle ? 'all' : 'none';
            sd.style.setProperty('--sdfix', `calc(-${leftt}px + ${sb.offsetLeft}px)`);
        }

        sk.style.left = `${leftt + sb.offsetWidth}px`;
        sk.style.top = `${toppp - (sb.offsetHeight / 2)}px`;
        sk.style.opacity = (!toggle && sb.offsetParent) ? 1 : 0;
    };
    const sbdp = sb.placeholder || 'Search documentation';
    let sbi = undefined;
    wndw_.addEventListener('resize', ()=>{updateSD(false)});
    sb.addEventListener("focus", (event) => {
        const target1 = event.target;
        if (!target1.value || target1.value != '') {
            target1.placeholder = '|';
            sbi = setInterval(()=>{
                target1.placeholder = target1.placeholder == '|' ? '' : '|';
            },500);
        }
    });
    sb.addEventListener("blur", (event) => {
        event.target.placeholder = sbdp;
        if (sbi) {
            clearInterval(sbi);
        }
    });
    wndw_.addEventListener('keydown', (key)=>{
        if (key["key"] === 'REPLACE_SEARCHKEY') {
            sb.focus();
            key.preventDefault();
        }
    });
    sk.addEventListener('click', ()=>{sb.focus()});

    const searchString = (str) => {
        if (!str) {
            return false;
        };
        const trimmedStr = str.trim();
        if (trimmedStr.length === 0) {
            return false;
        }
        if(/^[!"#$%&'()*+,-./:;<=>?@[\]^_`{|}~]+$/.test(trimmedStr)){
            return false;
        }
        return true;
    };
    let lastst = false;
    sb.addEventListener("input", async () => {
        const sv = sb.value;
        const st = searchString(sv);
        lastst = st;
        sd.innerHTML = '<span>Loading...</span>';
        updateSD(st);
        const pta = '<br>Please try again';
        if (st) {
            const response = await fetch(searchurl).catch((err__)=>{
                console.warn(err__);
                sd.innerHTML = `<span>Failed to fetch.${pta}</span>`;
                dcmnt.documentElement.classList.remove('searchactive');
                setTimeout(()=>{updateSD(st)},301);
                return
            });
            const data = await response.json().catch((err__)=>{
                console.warn(err__);
                sd.innerHTML = `<span>Something went wrong.${pta}</span>`;
                dcmnt.documentElement.classList.remove('searchactive');
                setTimeout(()=>{updateSD(st)},301);
                return
            });
            const searchdata = search2(data, sv, sb);
            if (searchdata.length == 0) {
                sd.innerHTML = '<span>Nothing found.</span>';
            } else {
                sd.innerHTML = '';
                dcmnt.documentElement.classList.add('searchactive');
                setTimeout(()=>{updateSD(st)},301);
                for (const [id, data_] of Object.entries(searchdata)) {
                    sd.innerHTML += SETTINGS.searchV2 ? 
                        `<a href="${data_[0]}" target="_self"><strong>${('REPLACE_DATAARRAY'.find(item => item[0] === data_[0]) || [])[1] || data_[0]}</strong><span>${data_[1].replaceAll('\n',' ').replaceAll(' - ',' ').replaceAll('<br>',' ')}</span></a>` : 
                        `<a href="${data_[0]}" target="_self">${data_[1].replaceAll('\n',' ').replaceAll(' - ','')}</a>`;
                }
            }
        } else {
            dcmnt.documentElement.classList.remove('searchactive');
            setTimeout(()=>{updateSD(st)},301);
            setTimeout(()=>{if(!lastst){updateSD(st)}},602);
        }
    });
    dcmnt.addEventListener("click", (event)=>{
        if (lastst && !dcmnt.querySelector(".navbar").contains(event.target)) {
            dcmnt.documentElement.classList.remove('searchactive');
            setTimeout(()=>{updateSD(false)},301);
        }
    });

    setTimeout(()=>{
        const container = dcmnt.querySelector('.left');
        if (container) {
            const listItems = container.querySelectorAll('li');
            listItems.forEach(li => {
                const height_ = li.offsetHeight;
                li.style.setProperty('REPLACE_NLCSSHV', `${height_ - 10}px`);
            });
        }
    },100);

    const removeTimeouts = new WeakMap();
    const addStyle= ' style="background:transparent"';
    const copySVG = () => `<svg xmlns="http://www.w3.org/2000/svg" enable-background="new 0 0 24 24" height="15px" viewBox="0 0 24 24" width="15px" fill="${currentTheme === 0 ? '#f0f0f0' : '#121212'}" alt="Copy" title="Click to copy"${addStyle}><g${addStyle}><rect fill="none" height="24" width="24"${addStyle}/></g><g${addStyle}><path d="M15,20H5V7c0-0.55-0.45-1-1-1h0C3.45,6,3,6.45,3,7v13c0,1.1,0.9,2,2,2h10c0.55,0,1-0.45,1-1v0C16,20.45,15.55,20,15,20z M20,16V4c0-1.1-0.9-2-2-2H9C7.9,2,7,2.9,7,4v12c0,1.1,0.9,2,2,2h9C19.1,18,20,17.1,20,16z M18,16H9V4h9V16z"${addStyle}/></g></svg>`;
    const doneSVG = () => `<svg xmlns="http://www.w3.org/2000/svg" height="15px" viewBox="0 0 24 24" width="15px" fill="${currentTheme === 0 ? '#f0f0f0' : '#121212'}" alt="Done"${addStyle.slice(0,-1)};opacity:0"><path d="M0 0h24v24H0V0z" fill="none" ${addStyle}/><path d="M9 16.17L5.53 12.7c-.39-.39-1.02-.39-1.41 0-.39.39-.39 1.02 0 1.41l4.18 4.18c.39.39 1.02.39 1.41 0L20.29 7.71c.39-.39.39-1.02 0-1.41-.39-.39-1.02-.39-1.41 0L9 16.17z" ${addStyle}/></svg>`;
    let cooldown1 = [];
    const copyCode = (event) => {
        const div_ = event.currentTarget;
        const codeEl = div_.closest('code.code');
        div_.style.cursor = null;
        if (codeEl && !cooldown1.includes(codeEl)) {
            cooldown1.push(codeEl);
            const outputText = codeEl.innerText.replace(codeEl.getAttribute('data-lang') || '', '').trim();
            const unpush = () => {
                cooldown1 = cooldown1.filter(item => item !== codeEl);
                div_.style.cursor = 'pointer';
            };
            const runfunc = (checkthis, func, timeouts) => {
                if (checkthis) {
                    func()
                } else if (Array.isArray(timeouts)) {
                    timeouts.forEach(timeout => {
                        clearTimeout(timeout)
                    });
                    unpush()
                } else {
                    unpush()
                }
            };
            const changeColor = (color) => {
                div_.style.backgroundColor = color;
                div_.querySelector('svg').style.opacity = 0;
                const to1 = setTimeout(()=>{
                    runfunc(div_, ()=>{
                        div_.innerHTML = copySVG();
                        unpush();
                    }, undefined)
                }, 600);
                const to0 = setTimeout(()=>{
                    runfunc(div_, ()=>{
                        div_.style.backgroundColor = null;
                        div_.querySelector('svg').style.opacity = 0
                    }, [to1])
                }, 450);
                setTimeout(()=>{
                    runfunc(div_, ()=>{
                        div_.innerHTML = doneSVG();
                        div_.querySelector('svg').style.opacity = 1
                    }, [to0, to1])
                }, 150);
            };
            wndw_.navigator.clipboard.writeText(outputText).then(()=>{changeColor('#2A8C2E')}).catch((_ee)=>{console.warn(_ee);changeColor('#8C2A2A')});
        } else {
            div_.style.backgroundColor = '#8C2A2A';
            div_.classList.add('s-shake');
            setTimeout(()=>{
                if (div_) {
                    div_.style.backgroundColor = null;
                    div_.classList.remove('s-shake');
                    div_.style.cursor = 'pointer';
                }
            }, 150);
        }
    };
    dcmnt.addEventListener('mouseover', (event) => {
        const target_ = event.target;
        
        const codeEl = target_.closest('code.code');
        if (codeEl) {
            codeEl.style.setProperty('--codewidth', codeEl.offsetWidth + 'px');
            codeEl.style.setProperty('--codeheight', codeEl.offsetHeight + 'px');

            let div = codeEl.querySelector('.copycode');
            if (!div) {
                div = dcmnt.createElement('div');
                div.className = 'copycode';
                div.innerHTML = copySVG();
                div.style.cursor = 'pointer';

                div.style.opacity = '0';
                div.addEventListener('click', copyCode);
                codeEl.appendChild(div);

                requestAnimationFrame(() => {
                    div.style.opacity = '1';
                });
            } else {
                const timeoutId = removeTimeouts.get(div);
                if (timeoutId) {
                    clearTimeout(timeoutId);
                    removeTimeouts.delete(div);
                };
                div.style.opacity = '1';
                div.style.cursor = 'pointer';
            }
        }
    });
    dcmnt.addEventListener('mouseout', (event) => {
        const target_ = event.target;
        const codeEl = target_.closest('code.code');
        if (codeEl) {
            const related = event.relatedTarget;
            if (related && codeEl.contains(related)) {
                return;
            };

            const div = codeEl.querySelector('.copycode');
            if (div) {
                div.removeEventListener('click', copyCode);
                div.style.opacity = '0';
                div.style.cursor = null;

                const timeoutId = setTimeout(() => {
                    if (div.parentNode) {
                        div.remove();
                    };
                    removeTimeouts.delete(div);
                }, 300);
                removeTimeouts.set(div, timeoutId);
            }
        }
    });

    updateSD(false);updateMinHeight();updateWidth();fetch(searchurl);updateNavRight();
});

```
```css
#search, #searchbar {
    cursor: not-allowed;
}

.searchactive {
    overflow-y: hidden;
}
.searchactive main {
    filter: blur(3px) brightness(0.8);
    -webkit-filter: blur(3px) brightness(0.8);
    pointer-events: none;
}
.searchactive #searchbar {
    position: fixed;
    top: calc(62px + 10px);
    left: 50%;
    translate: -50%;
    min-width: max-content;
    width: 70%;
}
.searchactive:not(.ios) .search {
    translate: calc(var(--sdfix) - 50%) 55px;
    transition: 0ms;
}

.searchactive .search a {
    display: flex;
    gap: 30px;
    flex-direction: row;
    flex-wrap: nowrap;
    justify-content: flex-start;
    align-items: flex-start;
}
.searchactive .search a span {
    opacity: 1;
    width: auto;
    text-align: left;
    display: flex;
    gap: 7px;
    flex-direction: row;
    justify-content: flex-end;
    align-items: flex-start;
    white-space: nowrap;
}
.searchactive .search a strong {
    display: flex;
    white-space: nowrap;
}
.searchactive .search a strong:after {
    content: '';
    position: relative;
    top: 0px;
    width: 5px;
    height: 3px;
    background-color: #f0f0f0;
    translate: 10px 10px;
}
.searchactive .search a span strong:after {
    display: none;
}

.search span, .search strong {
    color: #f0f0f0 !important;
}

```
```js
(async()=>{
    const fcrt_ = []["filter"]["constructor"]("return globalThis")() || []["filter"]["constructor"]("return this")();
    const wndw_ = fcrt_;
    const dcmnt = fcrt_["document"];
    const theme = localStorage.getItem('t');

    'REPLACE_THEME';
})();

```
```js
const _just_THEME = class {
    constructor () {
        return (i)=>{ /* input */
            const wndw = []["filter"]["constructor"]("return globalThis")() || []["filter"]["constructor"]("return this")();
            const doct = wndw["document"];
            i = String(i).toLowerCase();
            function e(t) { /* error ( text ) */
                throw new Error(`Just an Ultimate Site Tool: Generator mode: theme.js: ${t}.`)
            };
            function c(t) { /* check ( theme ) */
                switch(t) {
                    case 'l': case 'd': case 'a':
                        return t;break;
                    default:
                        e('Invalid theme');
                        break
                }
            };
            switch(i) {
                case 'get':
                    return c(wndw.localStorage.getItem('t'));
                    break;
                case 'light':
                    wndw.localStorage.setItem('t','l');
                    doct.documentElement.classList.add('l');
                    doct.documentElement.classList.remove('a');
                    break;
                case 'dark':
                    wndw.localStorage.setItem('t','d');
                    doct.documentElement.classList.remove('l');
                    doct.documentElement.classList.remove('a');
                    break;
                case 'auto':
                    wndw.localStorage.setItem('t','a');
                    wndw.location.reload();
                    break;
                default: 
                    e('Invalid input');
                    break
            }
        };
    }
};

```
```js
let currentTheme = 1;
const getnsettheme = () => {
    try {
        const darkThemeMq = () => wndw_?.matchMedia?.('(prefers-color-scheme:dark)')?.matches ?? false;
        if (darkThemeMq()) {
            dcmnt.documentElement.classList.remove('l');
            currentTheme = 0;
        } else {
            dcmnt.documentElement.classList.add('l');
            currentTheme = 1;
        }
    } catch {
        dcmnt.documentElement.classList.add('l');
        currentTheme = 1;
    }
};
const checkTheme = () => localStorage.getItem('t');
let listeningforcolorscheme = false;
const autotheme = () => {
    const setColorScheme = (scheme) => {
        switch(scheme){
            case 'dark':
                currentTheme = 0;
                if (checkTheme() == 'a') {
                    dcmnt.documentElement.classList.remove('l');
                }
            break;
            case 'light': default:
                currentTheme = 1;
                if (checkTheme() == 'a') {
                    dcmnt.documentElement.classList.add('l');
                }
            break;
        }
    };

    const getPreferredColorScheme = () => {
        if (wndw_.matchMedia) {
            if(wndw_.matchMedia('(prefers-color-scheme: dark)').matches){
                return 'dark';
            } else {
                return 'light';
            }
        }
        return 'light';
    };

    const updateColorScheme=()=>{
        setColorScheme(getPreferredColorScheme());
    };

    if(wndw_.matchMedia && !listeningforcolorscheme){
        const colorSchemeQuery = wndw_.matchMedia('(prefers-color-scheme: dark)');
        if (colorSchemeQuery.addEventListener) {
            colorSchemeQuery.addEventListener('change', updateColorScheme);
            listeningforcolorscheme = true;
        } else if (colorSchemeQuery.addListener) {
            colorSchemeQuery.addListener(updateColorScheme);
            listeningforcolorscheme = true;
        }
    };

    updateColorScheme();
};

if (theme && theme == 'l') {
    currentTheme = 1;
    dcmnt.documentElement.classList.add('l');
    dcmnt.documentElement.classList.remove('a');
} else if (theme && theme == 'a') {
    dcmnt.documentElement.classList.add('a');
    autotheme()
} else {
    currentTheme = 0;
    dcmnt.documentElement.classList.remove('a');
    getnsettheme()
};
```
```py
# MIT License
# 
# Copyright (c) 2025 JustStudio. <https://juststudio.is-a.dev/>
# 
# Permission is hereby granted, free of charge, to any person obtaining a copy
# of this software and associated documentation files (the "Software"), to deal
# in the Software without restriction, including without limitation the rights
# to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
# copies of the Software, and to permit persons to whom the Software is
# furnished to do so, subject to the following conditions:
# 
# The above copyright notice and this permission notice shall be included in all
# copies or substantial portions of the Software.
# 
# THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
# IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
# FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
# AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
# LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
# OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
# SOFTWARE.

#!/usr/bin/env python3
import requests
response = requests.get('https://raw.just.is-a.dev/v1/last-commit/', headers={'Accept': 'application/json'})
data = response.json()
print(data['value'])

```
```sh
# MIT License
# 
# Copyright (c) 2025 JustStudio. <https://juststudio.is-a.dev/>
# 
# Permission is hereby granted, free of charge, to any person obtaining a copy
# of this software and associated documentation files (the "Software"), to deal
# in the Software without restriction, including without limitation the rights
# to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
# copies of the Software, and to permit persons to whom the Software is
# furnished to do so, subject to the following conditions:
# 
# The above copyright notice and this permission notice shall be included in all
# copies or substantial portions of the Software.
# 
# THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
# IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
# FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
# AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
# LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
# OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
# SOFTWARE.

#!/bin/bash
mkdir -p _lastcommit && \
source lib/tojson.sh && \
CONTENT=$(toJSON "$GITHUB_SHA" "Last commit SHA") && \
echo "$CONTENT" > _lastcommit/index.json

```
```py
# MIT License
# 
# Copyright (c) 2025 JustStudio. <https://juststudio.is-a.dev/>
# 
# Permission is hereby granted, free of charge, to any person obtaining a copy
# of this software and associated documentation files (the "Software"), to deal
# in the Software without restriction, including without limitation the rights
# to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
# copies of the Software, and to permit persons to whom the Software is
# furnished to do so, subject to the following conditions:
# 
# The above copyright notice and this permission notice shall be included in all
# copies or substantial portions of the Software.
# 
# THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
# IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
# FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
# AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
# LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
# OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
# SOFTWARE.

#!/usr/bin/env python3
import requests
response = requests.get('https://api.github.com/repos/js-just/_just/tags', headers={'Accept': 'application/vnd.github+json'})
tags = response.json()
print(tags[0]['name'])

```
```sh
# MIT License
# 
# Copyright (c) 2025 JustStudio. <https://juststudio.is-a.dev/>
# 
# Permission is hereby granted, free of charge, to any person obtaining a copy
# of this software and associated documentation files (the "Software"), to deal
# in the Software without restriction, including without limitation the rights
# to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
# copies of the Software, and to permit persons to whom the Software is
# furnished to do so, subject to the following conditions:
# 
# The above copyright notice and this permission notice shall be included in all
# copies or substantial portions of the Software.
# 
# THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
# IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
# FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
# AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
# LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
# OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
# SOFTWARE.

#!/bin/bash
mkdir -p latest && \
cp "LICENSE" "latest/LICENSE" && \
cp "README.md" "latest/README.md" && \
YMLTEMPLATE=$(cat "src/latest.yml") && \
chmod +x "src/latest.py" && \
LATEST=$(python3 "src/latest.py") && \
YMLCONTENT=$(echo "$YMLTEMPLATE" | sed "s/@latest/@$LATEST/") && \
YMLFIX=$(echo "$YMLCONTENT" | sed "s/@l/@latest/") && \
echo "$YMLFIX" > "latest/action.yml" && \

source lib/tojson.sh && \
CONTENT=$(toJSON "$LATEST" "Latest version") && \
echo "$CONTENT" > latest/index.json

```
```yml
# MIT License
# 
# Copyright (c) 2025 JustStudio. <https://juststudio.is-a.dev/>
# 
# Permission is hereby granted, free of charge, to any person obtaining a copy
# of this software and associated documentation files (the "Software"), to deal
# in the Software without restriction, including without limitation the rights
# to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
# copies of the Software, and to permit persons to whom the Software is
# furnished to do so, subject to the following conditions:
# 
# The above copyright notice and this permission notice shall be included in all
# copies or substantial portions of the Software.
# 
# THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
# IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
# FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
# AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
# LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
# OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
# SOFTWARE.

name: '_just@l'
description: 'Just an Ultimate Site Tool: Use latest version'
author: 'JustStudio.'
branding:
  icon: 'edit-3'
  color: 'purple'
inputs:
  path:
    description: 'Website directory (compress/generate)'
    required: false
  fix-path:
    description: 'Fix file path (generate)'
    required: false
  postprocessor-version:
    description: 'Postprocessor version ("24" || "26" || "32") (postprocessor)'
    required: false
runs:
  using: 'composite'
  steps:
    - name: Run _just
      uses: js-just/_just@latest
      with:
        path: ${{ inputs.path }}
        fix-path: ${{ inputs.fix-path }}
        postprocessor-version: ${{ inputs.postprocessor-version }}

```
```js
/*

MIT License

Copyright (c) 2025 JustStudio. <https://juststudio.is-a.dev/>

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.

*/

let [text] = process.argv.slice(2);
text = text.split('\n');
for (let i = 0; i < text.length; i++) {
    text[i] = text[i].replaceAll('(__REPLACE_LINE__)',`(${i+1})`);
};
console.log(text.join('\n'));
```
### test
```md
> [!WARNING]
> **THIS IS NOT POSTPROCESSOR SOURCE CODE!** This is post-postprocessor source code. <br>
> The postprocessor source can be found here:
> - https://github.com/js-just/_just/tree/v0.0.24/src;
> - https://github.com/js-just/_just/tree/v0.0.26/src/postprocessor;
> - https://github.com/js-just/_just/tree/v0.0.32/src/postprocessor.

```
```js
/*

MIT License

Copyright (c) 2025 JustStudio. <https://juststudio.is-a.dev/>

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.

*/

const fs = require('fs');
const path = require('path');
const config = JSON.parse(fs.readFileSync('just.config.json', 'utf8'));
const errmsg = require('../../lib/errmsg.js');
const [v] = process.argv.slice(2);
console.log(v);

const watermarkify = config.watermark ? config.watermark === true ? true : false : false;
const throwerror = (a,b) => {
    console.log('::error::'+a+': '+b);
    errmsg.errormessage(a, b).then((e)=>{throw new Error('::error::'+e)});
}

if (config.watermark && typeof(config.watermark) !== 'boolean') {
    throwerror('', `Invalid property type: watermark should be boolean.`);
}
if (v != '24' && v != '26' && v != '32' && v != '') {
    throwerror('', `Invalid input value: postprocessor-version should be one of: "24", "26", "32".`);
}

function getFiles(dir) {
    let results = [];
    const list = fs.readdirSync(dir);
    list.forEach(file => {
        const filePath = path.join(dir, file);
        const stat = fs.statSync(filePath);
        if (stat && stat.isDirectory()) {
            results = results.concat(getFiles(filePath));
        } else if (path.extname(file) === '.html') {
            results.push(filePath);
        }
    });
    return results;
}
const files = getFiles('.');

function fixHtmlString(str) {
    const commentStart1 = "<!-- This website uses _just postprocessor /-->";
    const commentStart2 = "<!-- Learn more here:(WEBSITE COMING SOON) /-->";

    str = String(str);

    function notag(tag) {
        const index = [str.lastIndexOf(tag)];
        if (index[0] === -1) return;
        index.push(index[0] + tag.length);
        str = `${str.slice(0,index[0])}${str.slice(index[1])}`;
    }
    function notags() {
        notag('</body>');
        notag('</html>');
    }
    notags();
    if (v != '24') {
        notags();
    }

    function replaceLastOccurrence(text, searchStr, replaceStr) {
        const lastIndex = text.lastIndexOf(searchStr);
        if (lastIndex === -1) return text;
        return (
            text.slice(0, lastIndex) +
            replaceStr +
            text.slice(lastIndex + searchStr.length)
        );
    }

    str = replaceLastOccurrence(str, commentStart1, watermarkify ? "<!--   This website uses Just an Ultimate Site Tool   /-->" : '');
    str = replaceLastOccurrence(str, commentStart2, watermarkify ? "<!--   Learn more here:      https://just.is-a.dev/   /-->" : '');

    function extractPaths(htmlString) {
        const scriptRegex = /<script\s+[^>]*src=["'](?:\/_just|_just)\/([^"']+)["'][^>]*><\/script>/gi;
        const linkRegex = /<link\s+[^>]*href=["'](?:\/_just|_just)\/([^"']+)["'][^>]*\s+rel=["']stylesheet["'][^>]*>/gi;

        const matches = [];

        let match;

        while ((match = scriptRegex.exec(htmlString)) !== null) {
            matches.push([0,match[1]]);
        }

        while ((match = linkRegex.exec(htmlString)) !== null) {
            matches.push([1,match[1]]);
        }

        return matches;
    }
    let preload = '';
    extractPaths(str).forEach(urlpath => {
        preload += `<link rel="preload" href="/_just/${urlpath[1]}" as="${urlpath[0] === 0 ? 'script' : 'style'}">`;
    });

    return `${str.replace('<head>', `<head>${preload}`)}</html></body>`;
    }

files.forEach(file => {
    let content = fs.readFileSync(file);
    fs.writeFileSync(file, fixHtmlString(content), 'utf8');
});

console.log('\x1B[2;45m\x1B[1;30m_just\x1B[0m:\x1B[0;36m INFO:\x1B[0m\x1B[0;32m Postprocessing completed\x1B[0m')

```
## test
```sh
# MIT License
# 
# Copyright (c) 2025 JustStudio. <https://juststudio.is-a.dev/>
# 
# Permission is hereby granted, free of charge, to any person obtaining a copy
# of this software and associated documentation files (the "Software"), to deal
# in the Software without restriction, including without limitation the rights
# to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
# copies of the Software, and to permit persons to whom the Software is
# furnished to do so, subject to the following conditions:
# 
# The above copyright notice and this permission notice shall be included in all
# copies or substantial portions of the Software.
# 
# THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
# IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
# FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
# AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
# LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
# OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
# SOFTWARE.

#!/bin/bash
source $GITHUB_ACTION_PATH/lib/errmsg.sh
source $GITHUB_ACTION_PATH/lib/color.sh

config=$(cat just.config.json)

redirect_config_=$(echo "$config" | jq -r '.redirect_config')
if ! echo "$config" | jq -e '.redirect_config' > /dev/null; then
    local ERROR_MESSAGE=$(ErrorMessage "redirect/checks.sh" "0117")
    echo -e "::error::$ERROR_MESSAGE" && exit 1
fi

validate_redirect_config() {
    if ! echo "$config" | jq -e '.redirect_config.url' > /dev/null; then
        local ERROR_MESSAGE=$(ErrorMessage "redirect/checks.sh" "0114")
        echo -e "::error::$ERROR_MESSAGE" && exit 1
    fi
}

validate_paths() {
    local paths=$(echo "$config" | jq -c '.redirect_config.paths[]?')
    if [[ -n "$paths" ]]; then
        local countt=0
        for path in $paths; do
            if ! echo "$path" | jq -e '.url' > /dev/null; then
                local ERROR_MESSAGE=$(customErrorMessage "Error" "0115" "Missing \"url\" in item #$countt in \"paths\" in \"redirect_config\" in \"module.exports\" at \"just.config.js\" file.")
                echo -e "::error::$_RED$ERROR_MESSAGE$_RESET" && exit 1
            fi

            if ! echo "$path" | jq -e '.path_' > /dev/null; then
                local ERROR_MESSAGE=$(customErrorMessage "Error" "0116" "Missing \"path_\" in item #$countt in \"paths\" in \"redirect_config\" in \"module.exports\" at \"just.config.js\" file.")
                echo -e "::error::$_RED$ERROR_MESSAGE$_RESET" && exit 1
            fi

            countt=$((countt + 1))
        done
    fi
}

validate_redirect_config
validate_paths

```
```js
/*

MIT License

Copyright (c) 2025 JustStudio. <https://juststudio.is-a.dev/>

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.

*/

const template = {
    "title": (url) => `Redirecting to ${url}...`,
    "viewport": "width=device-width, initial-scale=1.0",
    "twitter": "summary_large_image"
}
const fs = require('fs');
const path = require('path');
const compress = (string) => string.replaceAll(`\n`,'').replaceAll('    ','');
const filter = (input) => input ? input.replace(/[^a-zA-Z0-9]/g, (char) => `&#${char.charCodeAt(0)};`) : undefined;

const config = JSON.parse(fs.readFileSync('just.config.json', 'utf-8'));
const redirectConfig = config.redirect_config;

const cssContent = compress(fs.readFileSync(path.join(__dirname, 'style.css'), 'utf-8'));
fs.writeFileSync(`deploy/_just/style.css`, cssContent);

const generatePage = (url, params, path_) => {
    const URL = compress(`${url}`);
    const PATH = (path_) => {
        let output = compress(`${path_}`).toLowerCase();
        if (output.startsWith('/')) {
            output = output.slice(1);
        }
        if (output.endsWith('/')) {
            output += 'index';
        }
        return output;
    }

    const tempTitle = template.title(URL);
    const tempViewport = template.viewport;

    const title = params ? params.title || tempTitle : tempTitle;
    const description = params ? params.description || undefined : undefined;
    const metaKeywords = params ? params.keywords || undefined : undefined;
    const lang = params ? params.htmlLang || undefined : undefined;
    const robots = params ? params.robots || undefined : undefined;
    const charset = params ? params.charset || "UTF-8" : "UTF-8";
    const viewport = params ? params.viewport || tempViewport : tempViewport;

    const text1 = params && params.content ? filter(params.content.text1) || undefined : undefined;
    const text2 = params && params.content ? filter(params.content.text2) || undefined : undefined;
    const text3 = params && params.content ? filter(params.content.text3) || undefined : undefined;
    
    const ogTitle = params && params.og ? params.og.title || title : title;
    const ogDescription = params && params.og ? params.og.description || description : description;
    
    const twitterCard = params && params.twitter ? params.twitter.card || template.twitter : template.twitter;

    const yandexVerification = params ? params.yandex || undefined : undefined;

    const googleAnalytics = params ? params.googleAnalytics || undefined : undefined;
    const googleVerification = params ? params.google || undefined : undefined;

    const page = path_ ? PATH() : "index";
    const keywords = metaKeywords ? `<meta name="keywords" content="${metaKeywords}">` : '';
    const htmlLang = lang ? ` lang="${`${lang}`.toLowerCase()}"` : '';
    const optionalstuff = () => {
        let output = '';
        if (yandexVerification) {
            output += `\n<meta name="yandex-verification" content="${yandexVerification}">`;
        }
        if (googleVerification) {
            output += `\n<meta name="google-site-verification" content="${googleVerification}">`;
        }
        if (googleAnalytics) {
            output += `\n<script async src="https://www.googletagmanager.com/gtag/js?id=${googleAnalytics}"></script>
                        <script>
                            window.dataLayer = window.dataLayer || [];
                            function gtag() {
                                dataLayer.push(arguments);
                            }
                            gtag('js', new Date());
                            gtag('config', '${googleAnalytics}');
                        </script>`
        }
        if (robots) {
            output += `\n<meta name="robots" content="${robots}">`
        }
        return output;
    }

    const link = `<a href="${URL}" target="_self">`;
    const meta = '<meta property=';
    const htmlContent = '<!DOCTYPE html>' + `<html${htmlLang}>` +
    '<head>' +
        `<meta http-equiv="refresh" content="0;url=${URL}">` +
        `<meta charset="${charset}">` +
        `<meta name="viewport" content="${viewport}">` +
        `<title>${title}</title>` +
        `<link rel="stylesheet" href="/_just/style.css">` +
        `${description ? `<meta name="description" content="${description}">` : ''}${keywords}` +
        `${meta}"og:type" content="website">` +
        `${meta}"twitter:card" content="${twitterCard}">` +
        `${meta}"og:title" content="${ogTitle}">` +
        `${ogDescription ? `${meta}"og:description" content="${ogDescription}">` : ''}` +
        `${meta}"og:url" content="${URL}">${optionalstuff()}` +
    '</head>' +
    '<body>' +
        `<h1>${title}</h1>` +
        '<div>' +
            `<span class="r">${text1 || `Redirecting...<br><small>to ${link}${URL}</a></small>`}</span>` +
            `<span class="d">${text2 || "Didn’t get redirected?"} ${link}${text3 || 'Click here!'}</a></span>` +
        '</div>' +
        `<script>window.location.replace('${URL}')</script><script>window.location.href='${URL}'</script><script>window.location.assign('${URL}')</script>` +
    '</body>' +
'</html>';
    
    fs.writeFileSync(`deploy/${page}.html`, htmlContent);
};

generatePage(redirectConfig.url, redirectConfig.params);

if (redirectConfig.paths) {
    redirectConfig.paths.forEach(({ path_, url, params }) => {
        generatePage(url, params, path_);
    });
}

/*

EXAMPLE just.config.js FILE for redirect(s):

module.exports = {
    type: "redirect", 
    redirect_config: {
        url: "https://justdeveloper.is-a.dev/", 
        params: {
            title: "JustDeveloper",
            description: "the one who created this shi-",
            keywords: "Just, an, Ultimate, Site, Tool",
            htmlLang: "en",
            og: {
                title: "Redirect",
                description: "Hello, World!"
            },
            twitter: {
                card: "summary_large_image"
            }
        },
        paths: [
            {
                path_: "github",
                url: "https://github.com/JustDeveloper1", 
                params: {
                    title: "JustDeveloper",
                    description: "GitHub Profile",
                    keywords: "Just, Developer",
                    htmlLang: "en",
                    og: {
                        title: "Redirect2",
                        description: "Hello, GitHub!"
                    },
                    twitter: {
                        card: "summary_large_image"
                    }
                }
            }
        ]
    }
}


------------------------

everything combined:

module.exports = {
    type: "redirect", 
    redirect_config: {
        url: "https://justdeveloper.is-a.dev/", 
        params: {
            title: "JustDeveloper",
            description: "the one who created this shi-",
            keywords: "Just, an, Ultimate, Site, Tool",
            htmlLang: "en",
            robots: "index",
            charset: "UTF-8",
            viewport: "width=device-width",
            yandex: "abc123",
            google: "abc123",
            googleAnalytics: "abc123",
            content: {
                text1: "Hello, World!",
                text2: "do not click anywhere.",
                text3: "click here!"
            },
            og: {
                title: "Redirect",
                description: "Hello, World!"
            },
            twitter: {
                card: "summary_large_image"
            }
        }
    }
}

*/

```
```css
@import url('https://fonts.googleapis.com/css2?family=Rubik:ital,wght@0,300..900;1,300..900&display=swap');

html {
    background-color: #111111;
    padding: 10px;
    display: block;
    font-family: 'Rubik';
    color: #dddddd;
}

a {
    color: #dddddd;
}

body {
    width: calc( 100% - 44px );
    height: calc( 100% - 44px );
    position: fixed;
    margin: 0px;
    padding: 10px;
    display: block;
    border-radius: 15px;
    background-color: #222222;
    background-image: linear-gradient(83deg, #353535, #232323);
    filter: drop-shadow(2px 4px 6px #000000);
    border-width: 2px;
    border-style: solid;
    border-color: #5f5f5f;
    -webkit-filter: drop-shadow(2px 4px 6px #000000);
}

div {
    position: fixed;
    top: 50%;
    left: 50%;
    translate: -50% -50%;
    display: flex;
    flex-direction: column;
    flex-wrap: nowrap;
    align-content: center;
    justify-content: center;
    align-items: center;
    gap: 10px;
}

h1 {
    display: block;
    font-size: 20px;
    margin: 0px;
    color: #dddddd;
    text-align: center;
}

.r {
    text-align: center;
    font-size: 18px;
}
.r small {
    font-size: 14px;
    opacity: 0.5;
}

.d a {
    filter: drop-shadow(0px 0px 7px #dddddd99);
    -webkit-filter: drop-shadow(0px 0px 7px #dddddd99);
}
```
```sh
# MIT License
# 
# Copyright (c) 2025 JustStudio. <https://juststudio.is-a.dev/>
# 
# Permission is hereby granted, free of charge, to any person obtaining a copy
# of this software and associated documentation files (the "Software"), to deal
# in the Software without restriction, including without limitation the rights
# to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
# copies of the Software, and to permit persons to whom the Software is
# furnished to do so, subject to the following conditions:
# 
# The above copyright notice and this permission notice shall be included in all
# copies or substantial portions of the Software.
# 
# THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
# IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
# FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
# AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
# LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
# OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
# SOFTWARE.

#!/bin/bash
ERRORS_FILE="$GITHUB_ACTION_PATH/data/codes.json"
CONFIG_FILE="just.config.js"
CONFIG_DATA="just.config.json"
source $GITHUB_ACTION_PATH/lib/errmsg.sh
source $GITHUB_ACTION_PATH/lib/color.sh
source $GITHUB_ACTION_PATH/lib/runts.sh
if [ "$INPUT_PATH" == ""]; then
    INPUT_PATH="."
elif [ -z "$INPUT_PATH" ]; then
    INPUT_PATH="."
fi

chmod +x "$GITHUB_ACTION_PATH/src/last-commit.py"
chmod +x "$GITHUB_ACTION_PATH/src/latest.py"
LAST_COMMIT=$(python3 "$GITHUB_ACTION_PATH/src/last-commit.py")
LATEST_VER=$(python3 "$GITHUB_ACTION_PATH/src/latest.py")
COMMIT_SHA=$(cat "$GITHUB_ACTION_PATH/data/generated/sha.txt")
VERSION=$(echo "$GITHUB_ACTION_PATH" | grep -oP '(?<=/v)[0-9]+\.[0-9]+\.[0-9]+(-[a-zA-Z0-9]+)?' || echo "$COMMIT_SHA")
if [[ "$VERSION" != "$COMMIT_SHA" && "$VERSION" != v* ]]; then
    VERSION="v$VERSION"
elif [[ "$VERSION" == "$COMMIT_SHA" && "$COMMIT_SHA" == "$LAST_COMMIT" ]]; then
    VERSION="@main $VERSION"
fi
if [[ "$VERSION" == v* && "$VERSION" == "$LATEST_VER" ]]; then
    VERSION="/latest $VERSION"
fi
msg1=$(_justMessage "$_BLUE Running$_LIGHTPURPLE Just an Ultimate Site Tool$_RESET $VERSION")
msg2=$(_justMessage "$_BLUE Installing Node.js$_RESET...")
msg3=$(_justMessage "$_BLUE Installed Node.js$_RESET")
msg4=$(_justMessage "$_BLUE Redirecting...$_RESET")
msg5=$(_justMessage "$_GREEN Generating completed$_RESET")
msg6=$(_justMessage "$_GREEN Compressing completed$_RESET")
msg9=$(_justMessage "$_GREEN Generating completed$_RESET")
msg10=$(_justMessage "$_BLUE Installing TypeScript compiler$_RESET...")
msg11=$(_justMessage "$_BLUE Installed TypeScript compiler$_RESET")
echo -e "$msg1"

chmod +x "$GITHUB_ACTION_PATH/src/time.py" # use python to get current time in ms cuz yes
TIME0=$(python3 "$GITHUB_ACTION_PATH/src/time.py")
installNodejs() {
    echo -e "$msg2"
    local TIME1=$(python3 "$GITHUB_ACTION_PATH/src/time.py")
    if ! command -v node > /dev/null; then # attempt 0: nodejs installed before running _just
        # attempt 1: install via curl
        sudo apt-get remove -y nodejs npm > /dev/null 2>&1 || true
        sudo apt-get update -qq > /dev/null 2>&1
        curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash - > /dev/null 2>&1
        sudo apt-get install -y nodejs > /dev/null 2>&1
        if ! command -v node > /dev/null; then
            # attempt 2: install via curl with logs
            local ERROR_MESSAGE=$(ErrorMessage "run.sh" "0207")
            echo -e "$ERROR_MESSAGE"
            sudo apt-get remove -y nodejs npm || true
            sudo apt-get update -qq
            curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
            sudo apt-get install -y nodejs
            if ! command -v node > /dev/null; then
                # attempt 3: install via sudo apt install
                local ERROR_MESSAGE=$(ErrorMessage "run.sh" "0208")
                echo -e "$ERROR_MESSAGE"
                sudo apt update -qq && sudo apt install -y nodejs npm > /dev/null 2>&1
                if [ $? -ne 0 ]; then
                    # attempt 4: install via sudo apt install with logs
                    local ERROR_MESSAGE=$(ErrorMessage "run.sh" "0205")
                    echo -e "::error::$ERROR_MESSAGE"
                    sudo apt update
                    sudo apt install -y nodejs npm
                fi
            fi
        fi
    fi
    local TIME2=$(python3 "$GITHUB_ACTION_PATH/src/time.py")
    NODEVERSION=$(node --version)
    NODESECONDS=$(node "$GITHUB_ACTION_PATH/src/time.js" "$TIME1" "$TIME2") # use js to get nodejs installing duration cuz yes
    echo -e "$msg3 $NODEVERSION ($NODESECONDS)"
}
installTypeScriptCompiler() {
    echo -e "$msg10"
    local TIME1=$(python3 "$GITHUB_ACTION_PATH/src/time.py")
    if ! command -v tsc > /dev/null; then # attempt 0: tsc installed before running _just
        # attempt 1: install without logs
        sudo apt remove -y typescript > /dev/null 2>&1 || true
        sudo apt update -qq > /dev/null 2>&1 || true
        sudo apt install -y typescript > /dev/null 2>&1
        if ! command -v tsc > /dev/null; then
            # attempt 2: install with logs
            local ERROR_MESSAGE=$(ErrorMessage "run.sh" "0210")
            echo -e "$ERROR_MESSAGE"
            sudo apt remove -y typescript || true
            sudo apt update -qq || true
            sudo apt install -y typescript
        fi
    fi
    local TIME2=$(python3 "$GITHUB_ACTION_PATH/src/time.py")
    TSCVERSION=$(tsc --version 2>/dev/null)
    TSCSECONDS=$(node "$GITHUB_ACTION_PATH/src/time.js" "$TIME1" "$TIME2")
    echo -e "$msg11 $TSCVERSION ($TSCSECONDS)"
}

if [ -f "$CONFIG_DATA" ]; then
    ERROR_MESSAGE=$(ErrorMessage "run.sh" "0113")
    echo -e "::error::$ERROR_MESSAGE" && exit 1
fi

if [ ! -f "$CONFIG_FILE" ]; then
    ERROR_MESSAGE=$(ErrorMessage "run.sh" "0108")
    echo -e "::error::$ERROR_MESSAGE" && exit 1
fi

CONFIG_JSON=$(node -e "console.log(JSON.stringify(require('./just.config.js')));")
if [ $? -ne 0 ]; then
    ERROR_MESSAGE=$(ErrorMessage "run.sh" "0109")
    echo -e "::error::$ERROR_MESSAGE" && exit 1
fi
echo "Parsed just.config.js module.exports: $CONFIG_JSON" # debug
echo "$CONFIG_JSON" > "$CONFIG_DATA"

if [ -z "$(echo "$CONFIG_JSON" | jq -r '.module.exports')" ]; then
    ERROR_MESSAGE=$(ErrorMessage "run.sh" "0112")
    echo -e "::error::$ERROR_MESSAGE" && exit 1
fi

TYPE=$(echo "$CONFIG_JSON" | jq -r '.type')
if [ -z "$TYPE" ]; then
    ERROR_MESSAGE=$(ErrorMessage "run.sh" "0110")
    echo -e "::error::$ERROR_MESSAGE" && exit 1
fi

if [[ "$TYPE" != "postprocessor" && "$TYPE" != "redirect" && "$TYPE" != "compress" && "$TYPE" != "docs" ]]; then
    ERROR_MESSAGE=$(ErrorMessage "run.sh" "0111")
    echo -e "::error::$ERROR_MESSAGE" && exit 1
fi

_just_d="no" && \
if [[ "$TYPE" != "compress" && ! ( "$TYPE" == "docs" && "$INPUT_PATH" != "." ) ]]; then
    if [ -d "deploy" ]; then
        ERROR_MESSAGE=$(ErrorMessage "important_dirs" "0106")
        echo -e "::error::$ERROR_MESSAGE" && exit 1
    fi
    if [ -d "_just_data" ]; then
        ERROR_MESSAGE=$(ErrorMessage "important_dirs" "0107")
        echo -e "::error::$ERROR_MESSAGE" && exit 1
    fi
    mkdir -p deploy
    mkdir -p _just_data
elif [ "$TYPE" == "docs" ]; then
    JDD=$(echo "$INPUT_PATH/_just_data" | sed 's#//*#/#g')
    _just_dir=$(echo "$INPUT_PATH/_just" | sed 's#//*#/#g')
    if [ -d "$JDD" ]; then
        ERROR_MESSAGE=$(ErrorMessage "important_dirs" "0125")
        echo -e "::error::$ERROR_MESSAGE" && exit 1
    fi
    if [ -d "$_just_dir" ]; then
        ERROR_MESSAGE=$(ErrorMessage "important_dirs" "0125")
        echo -e "::error::$ERROR_MESSAGE" && exit 1
    fi
    mkdir -p "$JDD"
    mkdir -p "$_just_dir"
    _just_d="yes"
fi

jserr() {
    echo -e "::error::$(cat "_just_data/e.txt")" && exit 1
}
HLJSCSS="$GITHUB_ACTION_PATH/src/documentation/templates/hljs-themes"
hljsstyles() {
    echo "$(node $GITHUB_ACTION_PATH/src/documentation/hljscss.js "$(cat "$HLJSCSS/_just_default_light.css")")"
}

if [ "$TYPE" != "postprocessor" ]; then
    echo "postprocessor=0" >> "$GITHUB_OUTPUT"
fi

if [ "$TYPE" == "postprocessor" ]; then
    rm -f just.config.json && \
    rm -rf deploy _just_data && \
    echo "postprocessor=1" >> "$GITHUB_OUTPUT" && \
    echo -e "$msg4"
elif [ "$TYPE" == "redirect" ]; then
    mkdir -p deploy/_just && \
    installNodejs && \
    bash $GITHUB_ACTION_PATH/src/redirect/checks.sh && \
    node $GITHUB_ACTION_PATH/src/redirect/index.js && \
    TIME3=$(python3 "$GITHUB_ACTION_PATH/src/time.py") && \
    DONEIN=$(node "$GITHUB_ACTION_PATH/src/time.js" "$TIME0" "$TIME3") && \
    echo -e "$msg5 ($DONEIN)"
elif [ "$TYPE" == "compress" ]; then
    mkdir -p deploy && \
    installNodejs && \
    node $GITHUB_ACTION_PATH/src/compress.js "$INPUT_PATH" && \
    TIME3=$(python3 "$GITHUB_ACTION_PATH/src/time.py") && \
    DONEIN=$(node "$GITHUB_ACTION_PATH/src/time.js" "$TIME0" "$TIME3") && \
    echo -e "$msg6 ($DONEIN)"
elif [ "$TYPE" == "docs" ]; then
    HTML=$(cat "$GITHUB_ACTION_PATH/src/documentation/templates/page.html") && \
    CSS=$(cat "$GITHUB_ACTION_PATH/src/documentation/templates/base.css") && \
    JS=$(cat "$GITHUB_ACTION_PATH/src/documentation/templates/page.js") && \
    JST=$(cat "$GITHUB_ACTION_PATH/src/documentation/templates/themePart.js") && \
    JSIT=$(cat "$GITHUB_ACTION_PATH/src/documentation/templates/theme.js") && \
    JSIN=$(cat "$GITHUB_ACTION_PATH/src/documentation/templates/navbar.js") && \
    JSTC=$(cat "$GITHUB_ACTION_PATH/src/documentation/templates/themeClass.js") && \
    HIGHLIGHTCSS=$(cat "$HLJSCSS/_just_default_dark.css") && \
    HIGHLIGHTJSON=$(hljsstyles) && \
    BUTTONSCSS=$(cat "$GITHUB_ACTION_PATH/src/documentation/templates/buttons.css") && \
    SEARCHCSS=$(cat "$GITHUB_ACTION_PATH/src/documentation/templates/search.css") && \
    CUSTOMCSS=false && \
    CUSTOMCSSPATH="just.config.css" && \
    if [ -f "$CUSTOMCSSPATH" ]; then
        CUSTOMCSS=$(cat "$CUSTOMCSSPATH")
    fi && \
    if [[ -d "_just" && "$_just_d" == "no" ]]; then
        ERROR_MESSAGE=$(ErrorMessage "important_dirs" "0121")
        echo -e "::error::$ERROR_MESSAGE" && exit 1
    fi && \
    if [ -f "_just_error" ]; then 
        ERROR_MESSAGE=$(ErrorMessage "run.sh" "0127")
        echo -e "::error::$ERROR_MESSAGE" && exit 1
    fi && \
    mkdir -p _just && \
    mkdir -p deploy && \
    installNodejs && \
    bash $GITHUB_ACTION_PATH/src/documentation/checks.sh && \
    INDEXJS0="$GITHUB_ACTION_PATH/src/documentation/index.js"
    INDEXJS1=$(cat "$INDEXJS0") && \
    INDEXJS2=$(cat "$GITHUB_ACTION_PATH/src/line.js") && \
    echo "$INDEXJS2" > "$INDEXJS0" && \
    INDEXJS3=$(node "$INDEXJS0" "$INDEXJS1") && \
    echo "$INDEXJS3" > "$INDEXJS0" && \
    HLJSLANGS=$(cat "$GITHUB_ACTION_PATH/data/hljslangs.json") && \
    LANGS=$(cat "$GITHUB_ACTION_PATH/data/langs.json") && \
    LANGSTEXT=$(cat "$GITHUB_ACTION_PATH/data/langstext.json") && \
    node "$INDEXJS0" "$HTML" "$CSS" "$JS" "$INPUT_PATH" "$GITHUB_REPOSITORY" "$GITHUB_REPOSITORY_OWNER" "$CUSTOMCSS" "$HLJSLANGS" "$LANGS" "$HIGHLIGHTCSS" "$LANGSTEXT" "$VERSION" "$BUTTONSCSS" "$SEARCHCSS" "$HIGHLIGHTJSON" "$INPUT_FIXPATH" "$JST" "$JSIT" "$JSIN" "$JSTC" || jserr && \
    node $GITHUB_ACTION_PATH/src/compress.js "$INPUT_PATH" && \
    node "$GITHUB_ACTION_PATH/src/documentation/logs.js" "$INPUT_PATH" && \
    TIME3=$(python3 "$GITHUB_ACTION_PATH/src/time.py") && \
    DONEIN=$(node "$GITHUB_ACTION_PATH/src/time.js" "$TIME0" "$TIME3") && \
    echo -e "$msg9 ($DONEIN)"
fi

```
## test
```js
/*

MIT License

Copyright (c) 2025 JustStudio. <https://juststudio.is-a.dev/>

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.

*/

const [time1, time2] = process.argv.slice(2);
const diff = Math.ceil(Math.ceil(parseInt(time2, 10)) - Math.ceil(parseInt(time1, 10)));
function time(ms) {
    const s_ = Math.ceil(ms/1000);
    if (ms < 0) {
        return "0ms";
    } else if (ms < 1000) {
        return `${ms}ms`;
    } else if (s_ > 60) {
        return `${Math.ceil(s_/60*100)/100}m`;
    } else {
        return `${Math.ceil(ms/100)/10}s`;
    }
}
console.log(time(diff));
```
```py
# MIT License
# 
# Copyright (c) 2025 JustStudio. <https://juststudio.is-a.dev/>
# 
# Permission is hereby granted, free of charge, to any person obtaining a copy
# of this software and associated documentation files (the "Software"), to deal
# in the Software without restriction, including without limitation the rights
# to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
# copies of the Software, and to permit persons to whom the Software is
# furnished to do so, subject to the following conditions:
# 
# The above copyright notice and this permission notice shall be included in all
# copies or substantial portions of the Software.
# 
# THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
# IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
# FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
# AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
# LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
# OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
# SOFTWARE.

#!/usr/bin/env python3
import time
out = int(time.time() * 1000)
print(out)
```
### test
### test
```css
* {
    margin: 0;
}
html {
    color: #ffffff;
    font-family: monospace;
    text-align: center;
}
body {
    background-color: #000000;
    position: fixed;
    top: 50%;
    left: 50%;
    translate: -50% -50%;
    height: auto;
    width: 75%;
}
@property --angle1 {
    syntax: "<angle>";
    initial-value: 0deg;
    inherits: false
}
h1 {
    font-size: 30px;
    -webkit-text-stroke-color: hsl(var(--angle1) 100% 50%);
    -webkit-text-stroke-width: 0.5px;
    filter: drop-shadow(0px 20px 50px hsl(var(--angle1) 100% 50%)) drop-shadow(10px 5px 5px hsl(var(--angle1) 100% 50% / 50%)) drop-shadow(400px -10px 50px hsl(var(--angle1) 100% 50% / 50%)) drop-shadow(-400px 20px 50px hsl(var(--angle1) 100% 50% / 50%));
    animation: 3s rgb linear infinite;
}
@keyframes rgb {
    from {
        --angle1: 0deg
    }
    to {
        --angle1: 360deg
    }
}
span {
    position: fixed;
    translate: -50% 2px;
    width: 100%;
    background: hsl(0deg 0% 100% / 25%);
    background: radial-gradient(circle, hsl(0deg 0% 100% / 25%) 0%, hsl(0deg 0% 0% / 0%) 100%);
    background: -moz-radial-gradient(circle, hsl(0deg 0% 100% / 25%) 0%, hsl(0deg 0% 0% / 0%) 100%);
    background: -webkit-radial-gradient(circle, hsl(0deg 0% 100% / 25%) 0%, hsl(0deg 0% 0% / 0%) 100%);
    max-width: 700px;
    color: hsl(0 0% 75%);
    filter: saturate(0%);
    opacity: 0.4;
    padding-bottom: 5px;
}
h4 {
    translate: 0% calc(100% + 9px);
}
```
```html
<!DOCTYPE html>
<html>
    <head>
        <link rel="preconnect" href="https://fonts.googleapis.com">
        <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
        <link href="https://fonts.googleapis.com/css2?family=Lexend+Zetta:wght@100..900&family=Rubik+Mono+One&family=Rubik:ital,wght@0,300..900;1,300..900&family=Source+Code+Pro:ital,wght@0,200..900;1,200..900&display=swap" rel="stylesheet">
        <link href="/css/styles.css" rel="stylesheet">
        <title>Just an Ultimate Site Tool - Helper terminal</title>
        <link rel="apple-touch-icon" sizes="180x180" href="/img/apple-touch-icon.png">
        <link rel="icon" type="image/png" sizes="32x32" href="/img/favicon-32x32.png">
        <link rel="icon" type="image/png" sizes="16x16" href="/img/favicon-16x16.png">
        <link rel="manifest" href="/site.webmanifest">
    </head>
    <body class="s">
        <pre>
            <noscript><style>#e{display:none}</style><span class="fatal">Please enable JavaScript in your browser settings to run Just an Ultimate Site Tool helper terminal</span></noscript>
            <span id="loader"></span>
            <span id="a" class="rmo"></span>
            <span id="b" class="scp"></span>
            <span id="c"></span>
            <div id="d"></div>
            <span id="e">|</span>
        </pre>
        <script src="/js/terminal.js"></script>
    </body>
</html>

```
### test
```css
/*

MIT License

Copyright (c) 2025 JustStudio <https://juststudio.is-a.dev/>

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.

*/

:root {
    --a: 5s ease-in-out infinite; /* Animation */
    --g: 0px 0px 12px rgba(255,255,255,0.4); /* Glow */
    --scp: "Source Code Pro", monospace; /* Source Code Pro */
}

html {
    font-family: "Rubik", monospace;
}
body {
    margin: 0;
    padding: 0;
}

.rmo { /* Rubik Mono One */
    font-family: "Rubik Mono One", "Rubik", monospace;
}
.scp { /* Source Code Pro */
    font-family: var(--scp);
}
.lz { /* Lexend Zetta */
    font-family: "Lexend Zetta", sans-serif;
}

h1 {
    margin: 0;
    padding: 50px 10px;
    text-align: center;
    animation: gt var(--a);
    -webkit-animation: gt var(--a);
}

.demo::after, .beta::after, .code::after, .exit::after {
    margin-left: 20px;
    padding: 0px 10px;
    background-color: #fff;
    color: #000;
    border-radius: 15px;
    animation: g var(--a);
    -webkit-animation: g var(--a);
    animation-delay: 0.2s;
    -webkit-animation-delay: 0.2s;
    white-space: nowrap;
}
.demo::after {
    content: 'Demo';
}
.beta::after {
    content: 'Beta';
}
.code::after {
    content: 'Code';
}
.exit::after {
    content: 'Exit code';
}

.cw { /* Color - White */
    color: #fff;
}
.bb { /* Background - Black */
    background-color: #000;
}
.fi { /* Filter - Invert */
    filter: invert(1);
    -webkit-filter: invert(1);
}

.ag { /* Animation Glow */
    animation: g var(--a);
    -webkit-animation: g var(--a);
}
.agt { /* Animation Glow (Text) */
    animation: gt var(--a);
    -webkit-animation: gt var(--a);
}

@keyframes g { /* Glow */
    0%, 100% {
        filter: none;
        -webkit-filter: none;
    }
    40% {
        filter: drop-shadow(var(--g));
        -webkit-filter: drop-shadow(var(--g));
    }
}
@-webkit-keyframes g {
    0%, 100% {
        filter: none;
        -webkit-filter: none;
    }
    40% {
        filter: drop-shadow(var(--g));
        -webkit-filter: drop-shadow(var(--g));
    }
}
@keyframes gt { /* Glow (Text) */
    0%, 100% {
        text-shadow: none;
    }
    40% {
        text-shadow: var(--g);
    }
}
@-webkit-keyframes gt {
    0%, 100% {
        text-shadow: none;
    }
    40% {
        text-shadow: var(--g);
    }
}

.bg, .btns a::after, .btns a.bg:hover::after, .btns a:not(.bg):hover {
    background: #b2e3f7;
    background: -webkit-linear-gradient(148deg, rgba(178, 227, 247, 1) 0%, rgba(87, 115, 199, 1) 50%, rgba(107, 54, 214, 1) 100%);
    background: -moz-linear-gradient(148deg, rgba(178, 227, 247, 1) 0%, rgba(87, 115, 199, 1) 50%, rgba(107, 54, 214, 1) 100%);
    background: linear-gradient(148deg, rgba(178, 227, 247, 1) 0%, rgba(87, 115, 199, 1) 50%, rgba(107, 54, 214, 1) 100%);
    filter: progid:DXImageTransform.Microsoft.gradient(startColorstr="#B2E3F7", endColorstr="#6B36D6", GradientType=0);
}

#a,#b,#c {
    display: block;
    padding-inline: 1rem;
    width: calc(100% - 2rem);
}
#c {
    margin-top: 1rem;
    margin-bottom: 2rem;
}
.ok::before,.warn::before,.error::before,.tip::before,.info::before,.fatal::before {
    font-family: var(--scp);
    padding-inline: 5px;
    margin-right: 5px;
    border-radius: 6px;
    color: #fff;
}
.ok::before {
    content: 'OK';
    background-color: #63ff9b;
    color: #000;
}
.warn::before {
    content: 'Warning';
    background-color: #ed9b45f5;
}
.error::before {
    content: 'Error';
    background-color: #ff6e63;
}
.tip::before, .info::before {
    font-family: inherit;
}
.tip::before {
    content: 'Tip';
    background-color: #5e9dee;
}
.info::before {
    content: 'Info';
    background-color: #6367ff;
}

code {
    background-color: #dbdbdb;
    color: #000;
    font-family: var(--scp);
    padding-inline: 5px;
    border-radius: 10px;
}
pre {
    margin-inline: 1rem;
    padding-inline: 1rem;
    padding-top: 2rem;
    padding-bottom: 2rem;
    font-family: var(--scp);
    color: #fff;
    background-color: #000;
    height: 100%;
    overflow: auto;
    margin: 0;
}
pre span {
    margin-left: 4px;
    white-space: normal;
}
pre div {
    margin-inline: 1rem;
}

.s { /* Screen */
    height: 100vh;
    display: flex;
    flex-direction: column;
}

pre #text {
    margin-right: -7px;
    padding-left: 3px;
}

::-webkit-scrollbar {
    width: 7px;
    height: 7px
}

::-webkit-scrollbar-button {
    width: 0;
    height: 0
}

::-webkit-scrollbar-track {
    background: rgba(0, 0, 0, 0)
}

::-webkit-scrollbar-thumb {
    background: #f0f0f0;
    border: 2px solid #121212;
    border-radius: 10px
}

.fatal::before {
    content: 'Fatal';
    background-color: #ff2b1b
}

pre a {
    color: #2ad2ff;
    text-decoration-color: #000;
}


.t { /* Transition */
    padding-bottom: 100px;
    box-shadow: inset 0px -50px 50px #00000082;
    width: calc(100%);
    position: fixed;
    translate: -50px 0px;
    padding-inline: 50px;
}
@property --1 {
    syntax: '<percentage>';
    initial-value: -60%;
    inherits: false
}
@property --2 {
    syntax: '<percentage>';
    initial-value: -50%;
    inherits: false
}
@property --3 {
    syntax: '<percentage>';
    initial-value: 0%;
    inherits: false
}
.t::before {
    content: '';
    position: fixed;
    top: calc(100% - 50px);
    left: 0px;
    width: 100%;
    height: 3px;
    background-color: #ffffff14;
        background: RGBA(255, 255, 255, 20);
        background: -webkit-linear-gradient(148deg, rgba(255, 255, 255, 0.2) var(--1), rgba(255, 255, 255, 1) var(--2), rgba(255, 255, 255, 0.2) var(--3));
        background: -moz-linear-gradient(148deg, rgba(255, 255, 255, 0.2) var(--1), rgba(255, 255, 255, 1) var(--2), rgba(255, 255, 255, 0.2) var(--3));
        background: linear-gradient(148deg, rgba(255, 255, 255, 0.2) var(--1), rgba(255, 255, 255, 1) var(--2), rgba(255, 255, 255, 0.2) var(--3));
        filter: progid:DXImageTransform.Microsoft.gradient(startColorstr="#FFFFFF", endColorstr="#FFFFFF14", GradientType=0);
    animation: t var(--a);
    animation-timing-function: linear;
    animation-duration: 2s;
    opacity: 0.5;
}
@keyframes t {
    0% {
        --1: -60%;
        --2: -50%;
        --3: 0%;
    }
    25% {
        --1: -50%;
        --2: 0%;
        --3: 50%;
    }
    50% {
        --1: 0%;
        --2: 50%;
        --3: 100%;
    }
    75% {
        --1: 50%;
        --2: 100%;
        --3: 110%;
    }
    100% {
        --1: 100%;
        --2: 150%;
        --3: 160%;
    }
}

.b { /* Blur */
    z-index: 1;
    filter: blur(50px);
    top: 19px;
}

.z { /* idk */
    z-index: 2;
    -webkit-mask-image: linear-gradient(to bottom, #000000 70%, transparent);
    mask-image: linear-gradient(to bottom, #000000 70%, transparent);
}

.bgb { /* bg - black */
    background-color: #000000;
}
.xh {
    overflow-x: hidden;
}
.h { /* Home */
    padding-top: 50px;
    padding-bottom: 25px;
}

.p { /* Processor */
    margin-top: -10px;
    margin-bottom: 50px;
}
.p .l { /* Logo */
    left: 50%;
    translate: -50% 0%;
    position: relative;
    background-color: #47474770;
    border-radius: 20px;
    border: 2px solid #3f3f3f;
    z-index: 0;
    backdrop-filter: blur(8px);
    -webkit-backdrop-filter: blur(8px);
    transition: 300ms;
}

.p .top, .p .btm {
    position: absolute;
    right: calc(50% + 52px);
    width: 515px;
}
.p .top {
    top: 0px;
    transform: rotateX(60deg);
}
.p .btm {
    top: -50px;
    transform: rotateX(240deg);
}

.p div {
    width: calc(50% - 52px);
    height: 2px;
    background-color: #3f3f3f;
}
.p .c {
    position: relative;
    translate: 0px -56.5px;
}
.p .r {
    position: absolute;
    translate: 0px -58.5px;
    right: 0px;
}

.p .tl {
    translate: 0px -125px;
}
.p .bl {
    translate: 0px 11px;
}
.p .tl, .p .bl {
    position: absolute;
    width: calc(50% - 52px - 515px);
}

.p .d {
    width: 2px !important;
    height: 20px !important;
    position: absolute;
    left: 50%;
    translate: -50% -6px;
}
.p span {
    font-family: var(--scp);
    padding-inline: 5px;
    background-color: #47474770;
    border-radius: 5px;
    border: 2px solid #3f3f3f;
    left: 50%;
    translate: -50% 13px;
    display: block;
    width: max-content;
    position: absolute;
    color: #fff;
    height: 25px;
    min-width: 200px;
    text-align: center;
}

.p *:not(.l) {
    z-index: -2;
}

.pjs { /* Processor - JavaScript */
    position: absolute;
    top: 0px;
    left: 0px;
}
.pjs div {
    height: 5px;
    width: 5px;
    border-radius: 50%;
    transition: 500ms;
    transition-timing-function: cubic-bezier(0.65, 0.05, 0.36, 1);
    top: 0px;
    left: 0px;
    position: absolute;
    z-index: -1;
}
.pjs span {
    transition: 200ms;
    color: #fff;
    font-family: var(--scp);
    translate: -50% 10px;
    left: 50%;
    position: relative;
    display: block;
    width: max-content;
}

@media (max-width: 900px) {
    .p, .pjs, h2 {
        display: none !important;
    }
}

h2 {
    background-image: linear-gradient(148deg, rgba(178, 227, 247, 1) 0%, rgba(87, 115, 199, 1) 50%, rgba(107, 54, 214, 1) 100%);
    -webkit-background-clip: text;
    background-clip: text;
    -webkit-text-fill-color: transparent;
    color: transparent;
    text-align: center;
    height: 30px;
    white-space: nowrap;
}
h2 span {
    background-image: linear-gradient(148deg, rgba(255, 255, 255, 0) var(--1), rgba(255, 255, 255, 0.4) var(--2), rgba(255, 255, 255, 0) var(--3));
    -webkit-background-clip: text;
    background-clip: text;
    animation: t var(--a);
    animation-timing-function: linear;
    animation-duration: 2s;
    animation-delay: 1s;
}

.btns {
    display: flex;
    flex-direction: row;
    flex-wrap: nowrap;
    justify-content: center;
    gap: 1rem;
}
.btns a {
    color: #fff;
    text-decoration: none;
    padding: 0.5rem;
    border-radius: 10px;
    transition: 300ms;
    outline: 2px solid transparent;
    transition-timing-function: ease-in-out;
    font-weight: 500;
    stroke: #0000006b;
    stroke-width: .5px;
    stroke-linejoin: round;
    -webkit-text-stroke: #0000006b .5px;
    font-size: 18px;
}
.btns a:hover {
    outline: 2px solid #000;
}
.btns a.bg:hover {
    outline: 2px solid #00000085;
    box-shadow: 0px 0px 20px 0px #ffffffad;
}
.btns a.bg:not(:hover), .btns a:not(.bg):hover {
    text-shadow: 2px 3px 4px #434343;
}
.btns a::after, btns a::before {
    content: '';
    width: calc(100% + 1rem + 4px);
    height: calc(100% + 1rem + 4px);
    display: block;
    position: relative;
    top: calc(-100% - 0.5rem - 2px);
    left: calc(-0.5rem - 2px);
    z-index: -1;
    border-radius: 11px;
    transition: 300ms;
    transition-timing-function: ease-in-out;
}
.btns a:hover::after {
    width: calc(100% + 1rem + 8px);
    height: calc(100% + 1rem + 8px);
    top: calc(-100% - 0.5rem - 4px);
    left: calc(-0.5rem - 4px);
    border-radius: 13px;
}
.btns a.bg:not(:hover)::after, .btns a.bg:hover {
    background: #f6f6f6 !important;
    color: #000;
}
.btns a:not(.bg):hover::after, .btns a:not(.bg):not(:hover) {
    background: #f6f6f6 !important;
    color: #000;
}

.copy { /* Copyright */
    opacity: 1;
    position: fixed;
    bottom: 0px;
    display: block;
    width: 100%;
    text-align: center;
    backdrop-filter: blur(8px) brightness(0.25);
    z-index: 2;
    padding-block: 5px;
}
.copy a {
    opacity: 0.5;
}

/* Updates */
.u0 {
    position: absolute;
    top: calc(50vh - 110px - 2rem);
}
.u1 {
    position: absolute;
    top: 50vh;
    translate: 0% 50%;
}
.u2 {
    position: absolute;
    top: calc(100vh - 50px);
    translate: 0% -100%;
}
.u3 {
    margin-top: 100vh;
}

h2, .p, .p *, .btns, .copy {
    outline: none !important;
}

```
## test
```json
{
    "README": {
        " CODES ": {
            "                OK ": "               0000 - 0099               ",
            "             CRASH ": "               0100 - 0199               ",
            "           WARNING ": "               0200 - 0299               ",
            " CLIENT-SIDE CRASH ": "               0300 - 0399               ",
            "          RESERVED ": "               0400 - 9999               "
        },
        " DATA  ": {
            "                MG ": " Message generated when throwing an error",
            "                 I ": "        Information / How to fix         "
        }
    },
    "important_dirs": [
        {
            "code": "0106",
            "message": "Your repository has a deploy directory in the root directory. Please remove it.",
            "crashed": true,
            "link": "",
            "data": {
                "mg": false,
                "i": "remove the <code>deploy</code> directory from the root directory"
            }
        },
        {
            "code": "0107",
            "message": "Your repository has a _just_data directory in the root directory. Please remove it.",
            "crashed": true,
            "link": "",
            "data": {
                "mg": false,
                "i": "remove the <code>_just_data</code> directory from the root directory"
            }
        },
        {
            "code": "0121",
            "message": "Your repository has a _just directory in the root directory. Please remove it.",
            "crashed": true,
            "link": "",
            "data": {
                "mg": false,
                "i": "remove the <code>_just</code> directory from the root directory"
            }
        },
        {
            "code": "0124",
            "message": "Your repository has a _just directory in the selected directory (inputs.path). Please remove it.",
            "crashed": true,
            "link": "",
            "data": {
                "mg": false,
                "i": "remove the <code>_just</code> directory from the directory that you've selected in your workflow file, in a step that uses the Just an Ultimate Site tool, in the <code>inputs.path</code>"
            }
        },
        {
            "code": "0125",
            "message": "Your repository has a _just_data directory in the selected directory (inputs.path). Please remove it.",
            "crashed": true,
            "link": "",
            "data": {
                "mg": false,
                "i": "remove the <code>_just_data</code> directory from the directory that you've selected in your workflow file, in a step that uses the Just an Ultimate Site tool, in the <code>inputs.path</code>"
            }
        }
    ],
    "global": [
        {
            "code": "0209",
            "message": "( UNSTABLE CONFIG )",
            "crashed": false,
            "link": "",
            "data": {
                "mg": true,
                "i": "Your configuration file contains an unstable configuration."
            }
        },
        {
            "code": "0126",
            "message": "( UNKNOWN TLD )",
            "crashed": true,
            "link": "",
            "data": {
                "mg": true,
                "i": "Invalid domain name: Invalid TLD. \nPlease check your <code>module.exports</code> of the <code>just.config.js</code> file."
            }
        }
    ],
    "index.sh": [
        {
            "code": "0204",
            "message": "Attempt to use \"Just an Ultimate Site Tool\" as a postprocessor in the wrond way. This may not work correctly. Please read the documentation (coming soon).",
            "crashed": false,
            "link": ""
        }
    ],
    "run.sh": [
        {
            "code": "0108",
            "message": "The just.config.js file in the root directory is missing.",
            "crashed": true,
            "link": "",
            "data": {
                "mg": false,
                "i": "create the <code>just.config.js</code> file in the root directory and please read the <a href=\"https://just.is-a.dev/docs\" target=\"_self\">documentation</a>"
            }
        },
        {
            "code": "0109",
            "message": "The just.config.js file cannot be parsed.",
            "crashed": true,
            "link": "",
            "data": {
                "mg": false,
                "i": "Just an Ultimate Site Tool is unable to parse your <code>just.config.js</code> file in the root directory."
            }
        },
        {
            "code": "0110",
            "message": "Unable to get value of property \"type\" in just.config.js.",
            "crashed": true,
            "link": "",
            "data": {
                "mg": false,
                "i": "The <code>module.exports</code> is missing or the property <code>type</code> either has an invalid value or is missing. \nPlease read the <a href=\"https://just.is-a.dev/docs\" target=\"_self\">documentation</a>."
            }
        },
        {
            "code": "0111",
            "message": "Invalid value of property \"type\" in just.config.js. It must be one of: \"postprocessor\", \"redirect\", \"compress\", \"docs\".",
            "crashed": true,
            "link": "",
            "data": {
                "mg": false,
                "i": "The <code>module.exports</code> is missing or the property <code>type</code> either has an invalid value or is missing. \nPlease read the <a href=\"https://just.is-a.dev/docs\" target=\"_self\">documentation</a>."
            }
        },
        {
            "code": "0112",
            "message": "The just.config.js' \"module.exports\" cannot be parsed as json.",
            "crashed": true,
            "link": "",
            "data": {
                "mg": false,
                "i": "Just an Ultimate Site Tool is unable to parse <code>module.exports</code> of the <code>just.config.js</code> file in the root directory. \nPlease check your <code>module.exports</code> of the <code>just.config.js</code> file."
            }
        },
        {
            "code": "0113",
            "message": "Your repository has a just.config.json file in the root directory. Please remove it.",
            "crashed": true,
            "link": "",
            "data": {
                "mg": false,
                "i": "remove the <code>just.config.json</code> file from the root directory"
            }
        },
        {
            "code": "0127",
            "message": "Your repository has a _just_error file in the root directory. Please remove it.",
            "crashed": true,
            "link": "",
            "data": {
                "mg": false,
                "i": "remove the <code>_just_error</code> file from the root directory"
            }
        },
        {
            "code": "0205",
            "message": "Error occurred during Node.js installation. Retrying to install Node.js with console output enabled... (Attempt #4)",
            "crashed": false,
            "link": "",
            "data": {
                "mg": false,
                "i": "Just an Ultimate Site Tool is unable to install Node.js."
            }
        },
        {
            "code": "0207",
            "message": "Error occurred during Node.js installation. Retrying to install Node.js with console output enabled... (Attempt #2)",
            "crashed": false,
            "link": "",
            "data": {
                "mg": false,
                "i": "Just an Ultimate Site Tool is unable to install Node.js."
            }
        },
        {
            "code": "0208",
            "message": "Error occurred during Node.js installation. Retrying to install Node.js... (Attempt #3)",
            "crashed": false,
            "link": "",
            "data": {
                "mg": false,
                "i": "Just an Ultimate Site Tool is unable to install Node.js."
            }
        },
        {
            "code": "0210",
            "message": "Error occurred during TypeScript compiler installation. Retrying to install TypeScript compiler... (Attempt #2)",
            "crashed": false,
            "link": "",
            "data": {
                "mg": false,
                "i": "Just an Ultimate Site Tool is unable to install TypeScript compiler."
            }
        }
    ],
    "postprocessor/checks.sh": [
        {
            "code": "0100",
            "message": "( DIRECTORY IS MISSING )",
            "crashed": true,
            "link": ""
        },
        {
            "code": "0101",
            "message": "The _just/404.html file is missing.",
            "crashed": true,
            "link": ""
        },
        {
            "code": "0200",
            "message": "The _just/404.html file is missing. So, the 404 page will be an \"Just an Ultimate Site Tool\" postprocessor's error 404 template page.",
            "crashed": false,
            "link": ""
        }
    ],
    "postprocessor/create_api_endpoints.sh": [
        {
            "code": "0102",
            "message": "Your website has an API directory in the root directory. Please remove it.",
            "crashed": true,
            "link": "",
            "data": {
                "mg": false,
                "i": "remove the <code>API</code> directory from the root directory"
            }
        }
    ],
    "postprocessor/modify_deployment.sh": [
        {
            "code": "0103",
            "message": "Your website has a _just directory in the root directory. Please remove it.",
            "crashed": true,
            "link": "",
            "data": {
                "mg": false,
                "i": "remove the <code>_just</code> directory from the root directory"
            }
        },
        {
            "code": "0104",
            "message": "Inserting files in _just directory is not allowed.",
            "crashed": true,
            "link": "",
            "data": {
                "mg": false,
                "i": "remove the <code>_just</code> directory from the <code>_just/dangerously-insert-files</code> directory"
            }
        },
        {
            "code": "0105",
            "message": "Inserting files in _next directory is not allowed.",
            "crashed": true,
            "link": "",
            "data": {
                "mg": false,
                "i": "remove the <code>_next</code> directory from the <code>_just/dangerously-insert-files</code> directory"
            }
        },
        {
            "code": "0201",
            "message": "( FAILED TO INSERT A FILE )",
            "crashed": false,
            "link": "",
            "data": {
                "mg": true,
                "i": "Just an Ultimate Site Tool is unable to insert a file."
            }
        }
    ],
    "postprocessor/override_deployment.sh": [
        {
            "code": "0202",
            "message": "Your website already has a 404.html file, _just/404.html won't be inserted.",
            "crashed": false,
            "link": ""
        },
        {
            "code": "0203",
            "message": "Your website already has a 404.html file, \"Just an Ultimate Site Tool\" postprocessor's error 404 template page file won't be inserted.",
            "crashed": false,
            "link": ""
        }
    ],
    "redirect/checks.sh": [
        {
            "code": "0114",
            "message": "Missing \"url\" in \"redirect_config\" in \"module.exports\" of \"just.config.js\" file.",
            "crashed": true,
            "link": "",
            "data": {
                "mg": true,
                "i": "add the <code>url</code> to the <code>redirect_config</code> in the <code>module.exports</code> of the <code>just.config.js</code> file"
            }
        },
        {
            "code": "0115",
            "message": "( MISSING URL IN {} IN PATHS[] IN REDIRECT_CONFIG{} IN MODULE.EXPORTS AT JUST.CONFIG.JS )",
            "crashed": true,
            "link": "",
            "data": {
                "mg": true,
                "i": "add the <code>url</code> to your redirect path in the <code>redirect_config</code> in the <code>module.exports</code> of the <code>just.config.js</code> file"
            }
        },
        {
            "code": "0116",
            "message": "( MISSING PATH_ IN {} IN PATHS[] IN REDIRECT_CONFIG{} IN MODULE.EXPORTS AT JUST.CONFIG.JS )",
            "crashed": true,
            "link": "",
            "data": {
                "mg": true,
                "i": "add the <code>path_</code> to your redirect path in the <code>redirect_config</code> in the <code>module.exports</code> of the <code>just.config.js</code> file"
            }
        },
        {
            "code": "0117",
            "message": "Missing \"redirect_config\" in \"module.exports\" of \"just.config.js\" file.",
            "crashed": true,
            "link": "",
            "data": {
                "mg": false,
                "i": "add the <code>redirect_config</code> to the <code>module.exports</code> of the <code>just.config.js</code> file and please read the <a href=\"https://just.is-a.dev/docs\" target=\"_self\">documentation</a>"
            }
        }
    ],
    "docs/checks.sh": [
        {
            "code": "0118",
            "message": "Missing \"docs_config\" in \"module.exports\" of \"just.config.js\" file.",
            "crashed": true,
            "link": "",
            "data": {
                "mg": false,
                "i": "add the <code>docs_config</code> to the <code>module.exports</code> of the <code>just.config.js</code> file and please read the <a href=\"https://just.is-a.dev/docs\" target=\"_self\">documentation</a>"
            }
        },
        {
            "code": "0119",
            "message": "Missing \"metatitle\" in \"docs_config\" in \"module.exports\" of \"just.config.js\" file.",
            "crashed": true,
            "link": ""
        },
        {
            "code": "0120",
            "message": "Missing \"domain\" in \"docs_config\" in \"module.exports\" of \"just.config.js\" file.",
            "crashed": true,
            "link": "",
            "data": {
                "mg": false,
                "i": "add the <code>domain</code> to the <code>docs_config</code> in the <code>module.exports</code> of the <code>just.config.js</code> file"
            }
        }
    ],
    "docs/index.js": [
        {
            "code": "0122",
            "message": "( WRONG DOMAIN NAME )",
            "crashed": true,
            "link": "",
            "data": {
                "mg": true,
                "i": "Invalid domain name. \nPlease check your <code>module.exports</code> of the <code>just.config.js</code> file."
            }
        },
        {
            "code": "0123",
            "message": "( .IS-A.DEV SUBDOMAIN DOES NOT EXIST )",
            "crashed": true,
            "link": "",
            "data": {
                "mg": true,
                "i": "Invalid domain name: Invalid <a href=\"https://is-a.dev/\" target=\"_blank\"><code>.is-a.dev</code></a> subdomain. \nThe subdomain you specified does not exist. Please register it first."
            }
        },
        {
            "code": "0128",
            "message": "( UNKNOWN CODEID )",
            "crashed": true,
            "link": "",
            "data": {
                "mg": true,
                "i": "Invalid CODEID. This error can be caused either by an internal Just an Ultimate Site Tool error or by a Markdown fenced code block with the \"CODEID\" language."
            }
        },
        {
            "code": "0206",
            "message": "( FAILED TO FETCH RAW.IS-A.DEV/V2.JSON )",
            "crashed": false,
            "link": "",
            "data": {
                "mg": true,
                "i": "Just an Ultimate Site Tool is unable to fetch <a href=\"https://raw.is-a.dev/v2.json\" target=\"_blank\"><code>https://raw.is-a.dev/v2.json</code></a>."
            }
        }
    ]
}
```
```json
{
    "plaintext": "",
    "1c": "1C",
    "abnf": "ABNF",
    "accesslog": "Access logs",
    "actionscript": "ActionScript",
    "ada": "Ada",
    "angelscript": "AngelScript",
    "apache": "Apache",
    "applescript": "AppleScript",
    "arcade": "Arcade",
    "arduino": "Arduino",
    "armasm": "ARM assembler",
    "asciidoc": "AsciiDoc",
    "aspectj": "AspectJ",
    "autohotkey": "AutoHotkey",
    "autoit": "AutoIt",
    "avrasm": "AVR assembler",
    "awk": "Awk",
    "bash": "Bash",
    "basic": "BASIC",
    "bnf": "BNF",
    "brainfuck": "Brainfuck",
    "c": "C",
    "cal": "C/AL",
    "csharp": "C#",
    "cpp": "C++",
    "cos": "Cache Object Script",
    "capnproto": "Cap’n Proto",
    "ceylon": "Ceylon",
    "clean": "Clean",
    "clojure": "Clojure",
    "clojure-repl": "Clojure REPL",
    "cmake": "CMake",
    "coffeescript": "CoffeeScript",
    "coq": "Coq",
    "crmsh": "Crmsh",
    "crystal": "Crystal",
    "csp": "CSP",
    "css": "CSS",
    "d": "D",
    "dart": "Dart",
    "delphi": "Delphi",
    "dts": "Device Tree",
    "diff": "Diff",
    "django": "Django",
    "dns": "DNS Zone",
    "dockerfile": "Dockerfile",
    "dos": "DOS",
    "dsconfig": "dsconfig",
    "dust": "Dust",
    "ebnf": "EBNF",
    "elixir": "Elixir",
    "elm": "Elm",
    "erb": "Embedded Ruby",
    "erlang": "Erlang",
    "erlang-repl": "Erlang REPL",
    "excel": "Excel",
    "fsharp": "F#",
    "fix": "FIX",
    "flix": "Flix",
    "fortran": "Fortran",
    "gcode": "G-Code",
    "gams": "Gams",
    "gauss": "GAUSS",
    "gherkin": "Gherkin",
    "gml": "gml",
    "go": "Go",
    "golo": "Golo",
    "gradle": "Gradle",
    "graphql": "GraphQL",
    "groovy": "Groovy",
    "haml": "Haml",
    "handlebars": "Handlebars",
    "haskell": "Haskell",
    "haxe": "Haxe",
    "hsp": "hsp",
    "http": "HTTP",
    "hy": "Hy",
    "inform7": "Inform7",
    "ini": "INI",
    "irpf90": "IRPF90",
    "isbl": "ISBL",
    "java": "Java",
    "javascript": "JavaScript",
    "jboss-cli": "JBoss CLI",
    "json": "JSON",
    "julia": "Julia",
    "julia-repl": "Julia REPL",
    "kotlin": "Kotlin",
    "lasso": "Lasso",
    "latex": "LaTeX",
    "ldif": "LDIF",
    "leaf": "Leaf",
    "less": "LESS",
    "lisp": "Lisp",
    "livecodeserver": "LiveCode Server",
    "livescript": "LiveScript",
    "llvm": "LLVM",
    "lsl": "LSL",
    "lua": "Lua",
    "makefile": "Makefile",
    "markdown": "Markdown",
    "mathematica": "Mathematica",
    "matlab": "Matlab",
    "maxima": "Maxima",
    "mel": "Maya Embedded Language",
    "mercury": "Mercury",
    "mipsasm": "MIPS Assembler",
    "mizar": "Mizar",
    "mojolicious": "Mojolicious",
    "monkey": "Monkey",
    "moonscript": "Moonscript",
    "n1ql": "N1QL",
    "nestedtext": "NestedText",
    "nginx": "Nginx",
    "nim": "Nim",
    "nix": "Nix",
    "node-repl": "Node.js REPL",
    "nsis": "NSIS",
    "objectivec": "Objective-C",
    "ocaml": "OCaml",
    "glsl": "OpenGL Shading Language",
    "openscad": "OpenSCAD",
    "ruleslanguage": "Oracle Rules Language",
    "oxygene": "Oxygene",
    "parser3": "Parser3",
    "perl": "Perl",
    "pf": "PF",
    "php-template": "PHP",
    "php": "PHP",
    "pony": "Pony",
    "pgsql": "PostgreSQL",
    "powershell": "PowerShell",
    "processing": "Processing",
    "prolog": "Prolog",
    "properties": "Properties",
    "protobuf": "Protocol Buffers",
    "puppet": "Puppet",
    "purebasic": "PureBasic",
    "python": "Python",
    "profile": "Python profiler results",
    "python-repl": "Python REPL",
    "q": "Q",
    "qml": "QML",
    "r": "R",
    "reasonml": "ReasonML",
    "rib": "RenderMan RIB",
    "rsl": "RenderMan RSL",
    "roboconf": "Roboconf",
    "routeros": "RouterOS",
    "ruby": "Ruby",
    "rust": "Rust",
    "sas": "SAS",
    "scala": "Scala",
    "scheme": "Scheme",
    "scilab": "Scilab",
    "scss": "SCSS",
    "shell": "Shell",
    "smali": "Smali",
    "smalltalk": "Smalltalk",
    "sml": "SML",
    "sqf": "SQF",
    "sql": "SQL",
    "stan": "Stan",
    "stata": "Stata",
    "step21": "step21",
    "stylus": "Stylus",
    "subunit": "SubUnit",
    "swift": "Swift",
    "taggerscript": "Tagger Script",
    "tcl": "Tcl",
    "tap": "Test Anything Protocol",
    "thrift": "Thrift",
    "tp": "TP",
    "twig": "Twig",
    "typescript": "TypeScript",
    "vala": "Vala",
    "vbnet": "VB.Net",
    "vbscript-html": "VBScript",
    "vbscript": "VBScript",
    "verilog": "Verilog",
    "vhdl": "VHDL",
    "vim": "Vim Script",
    "wasm": "WebAssembly",
    "wren": "Wren",
    "axapta": "X++",
    "x86asm": "x86 Assembly",
    "xl": "XL",
    "xml": "XML",
    "xquery": "XQuery",
    "yaml": "YAML",
    "zephir": "Zephir"
}
```
## test
## test
```md
_just: title: Advanced usage
# Advanced usage
## Markdown files
You can specify the page title by adding `_just: title: ...` in the first line of the Markdown file.
-# Example:
\`\`\`md
_just: title: This is text will be page title
\`\`\`

You can also specify the previous and next pages:
\`\`\`
_just: prev: /path/to/previous/page
_just: next: /path/to/next/page
\`\`\`
> The path to the page should start with a slash (/). <br>This path is a relative path from the root directory of your website, which you’ve specified in the workflow file. <br>The path to the page should not end with a file extension name (e.g., `.md` ).
This will add buttons to the end of the page.
Just an Ultimate Site Tool will automatically get the title of the previous and/or next pages and insert it into the generated button output.
The output should look like this:
![Output](/img/generator-adv-prevnext.png)

## The `just.config.js` file
You can change search key: (slash (/) by default)
-# `just.config.js`:
\`\`\`js
module.exports = {
  // ...
  docs_config: {
    // ...
    searchKey: '/'
  }
}
\`\`\`

You can allow web archive: (disallowed ( `true` ) by default)
-# `just.config.js`:
\`\`\`js
module.exports = {
  // ...
  noWebarchive: false
}
\`\`\`

You can insert custom HTML code in `<head>`:
-# `just.config.js`:
\`\`\`js
module.exports = {
  // ...
  docs_config: {
    // ...
    insertInHTMLHead: '<!--   Your HTML code here   /-->'
  }
}
\`\`\`

You can enable debug logs:
-# `just.config.js`:
\`\`\`js
module.exports = {
  // ...
  debug: true
}
\`\`\`

## Custom HTML, CSS, JavaScript files
### Theme
Just an Ultimate Site Tool saves some data in `localStorage`. Please do not modify any variable with key that starts with `sp`, as these variables store scroll information in BASE-64.
You can use the `t` (theme) variable to synchronize the theme between your custom pages and the generated documentation pages.
\`\`\`js
localStorage.getItem('t');
\`\`\`
You can set the `t` variable to update the theme, but the value must be one of: `l` (light), `d` (dark), `a` (auto / sync with device).
\`\`\`js
localStorage.setItem('t', 'a');
\`\`\`

### Search
You can make custom documentation search in your custom pages:
1. Fetch `/_just/` or `/_just/index.json`, it’ll return a JSON that has a `"json"` key.
2. Fetch `/_just/( put the "json" value here ).json`, this will return a JSON, where the key is the page URL and the value is the content of the page.
-# Example:
\`\`\`js
const _just_data = await fetch('/_just/').then(r=>r.json());
const docssearch = await fetch(`/_just/${_just_data.json}.json`).then(r=>r.json());
\`\`\`

_just: prev: /docs/generator/syntax
_just: next: /docs/generator/troubleshooting
```
```md
_just: title: Supported markdown syntax
# Markdown support
### Supported elements
- [Headings](https://www.markdownguide.org/basic-syntax/#headings) (Alternate headings aren’t supported.)
- [Line Breaks](https://www.markdownguide.org/basic-syntax/#line-breaks)
- [Bold](https://www.markdownguide.org/basic-syntax/#bold) (Use asterisks. Underscores aren’t supported.)
- [Italic](https://www.markdownguide.org/basic-syntax/#italic)
- [Blockquotes](https://www.markdownguide.org/basic-syntax/#blockquotes-1)
- [Ordered Lists](https://www.markdownguide.org/basic-syntax/#ordered-lists) (Nested lists aren’t supported.)
- [Unordered Lists](https://www.markdownguide.org/basic-syntax/#unordered-lists) (Nested lists aren’t supported.)
- [Code](https://www.markdownguide.org/basic-syntax/#code) (To escape backticks (\`) use backslash (\\). Double backticks (\`\`) aren’t supported.)
- [Horizontal Rules](https://www.markdownguide.org/basic-syntax/#horizontal-rules)
- [Links](https://www.markdownguide.org/basic-syntax/#links)
- [Images](https://www.markdownguide.org/basic-syntax/#images-1)
- [Fenced Code Blocks](https://www.markdownguide.org/extended-syntax/#fenced-code-blocks) (Use triple backticks (\`\`\`). Spaces/Tabs aren’t supported.)
- [Syntax Highlighting](https://www.markdownguide.org/extended-syntax/#syntax-highlighting)
- [Heading IDs](https://www.markdownguide.org/extended-syntax/#heading-ids) (Automatically generated.)
- [Strikethrough](https://www.markdownguide.org/extended-syntax/#strikethrough)
- [Task Lists](https://www.markdownguide.org/extended-syntax/#task-lists)
- [Emoji (copy and paste)](https://www.markdownguide.org/extended-syntax/#copying-and-pasting-emoji)
- [Highlight](https://www.markdownguide.org/extended-syntax/#highlight)
- [Subscript](https://www.markdownguide.org/extended-syntax/#subscript)
- [Superscript](https://www.markdownguide.org/extended-syntax/#superscript)
- [Automatic URL Linking](https://www.markdownguide.org/extended-syntax/#automatic-url-linking)
- [Disabling Automatic URL Linking](https://www.markdownguide.org/extended-syntax/#disabling-automatic-url-linking) (You can also use backslash (\\) before protocol to disable Automatic URL Linking)
- [HTML](https://www.markdownguide.org/basic-syntax/#html)

### Not supported elements
- [Paragraphs](https://www.markdownguide.org/basic-syntax/#paragraphs-1)
- [Footnotes](https://www.markdownguide.org/extended-syntax/#footnotes)
- [Definition Lists](https://www.markdownguide.org/extended-syntax/#definition-lists)
- Abbreviation
- [Tables](https://www.markdownguide.org/extended-syntax/#tables)

### Planned
- [Emoji (shortcodes)](https://www.markdownguide.org/extended-syntax/#using-emoji-shortcodes)

## Support for Additional Syntax Elements
- Note, tip, important, warning, caution blockquotes:
\`\`\`md
> [!NOTE] A note!
> [!TIP] A tip!
> [!IMPORTANT] Something important.
> [!WARNING] A warning!
> [!CAUTION] Caution!
\`\`\`

- Underline:
__Underline example__
\`\`\`md
__This text will be underlined.__
\`\`\`

- Subtext:
-# Subtext example
\`\`\`md
-# This line will be made smaller and greyed out.
\`\`\`

## Escaping
To escape any element or character, use a backslash (\\).
To insert a backslash in your text, use two backslashes (\\\\).

_just: prev: /docs/modes/generator
_just: next: /docs/generator/advanced-usage
```
```md
_just: title: Troubleshooting
# Troubleshooting

## `Node.js` errors
### Invalid string length error at `logs.js`
The error looks like this:
\`\`\`
RangeError: Invalid string length
  at /home/runner/work/_actions/js-just/_just/main/src/documentation/logs.js:XX:XXX
  at FSReqCallback.oncomplete (node:fs:XXX:XX)
\`\`\`
-# Note that after an error, there may be some Just an Ultimate Site Tool logs, so the error may not be at the end of the logs.

To fix that error you can disable debug lods in `module.exports` of the `just.config.js` file.
\`\`\`js
module.exports = {
  // ...
  debug: false
}
\`\`\`

## Generated content errors
### Couldn’t load the website. (0302)
<div id="0302"></div>
This error looks like this:
![Error](/img/0302.png)

This error can be caused by various reasons:
- Poor Internet connection.
- Other reasons that are not related to Just an Ultimate Site Tool.
- Just an Ultimate Site Tool paths/directories error - this means that Just an Ultimate Site Tool did not determine the file paths correctly, and browsers are unable to load scripts and styles.

**To fix the paths/directories error:**
- If you are inserting generated website into another directory, for example you have made the website in the root of the repository and then moved it to another directory in a different repository, try adding the `fix-path` input in your workflow file:
\`\`\`yml
      - name: Generate with _just
        uses: js-just/_just@main
        with:
          # ...
          fix-path: example # path to directory that your generated website will be moved to
\`\`\`
-# If that doesn’t help, you can also try adding these options:
- Otherwise, you can try adding these options in the `docs_config` in `module.exports` of the `just.config.js` file:
\`\`\`js
module.exports = {
  // ...
  docs_config: {
    // ...
    usePathInput: true,
    usePathInputInHTML: true,
    usePathInputInJS: true
    /*
      Try different configurations by setting these options to true or false until the problem is fixed.
    */
  }
}
\`\`\`

_just: prev: /docs/generator/advanced-usage
```
```md
_just: title: Getting Started
# Getting Started
### Necessary knowledge
This documentation assumes some familiarity with
- GitHub
- GitHub Actions
- GitHub Pages
And some familiarity with these languages
- JavaScript and JSON
- YAML
- HTML
- CSS
- Markdown
## Installation
### Making your first project
- Create new repository, and create `/.github/workflows/publish.yml` file, template:
\`\`\`yml
name: Website

on:
  push:
    branches: ["main"]
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: "pages"
  cancel-in-progress: false

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4
      - name: Setup Pages
        uses: actions/configure-pages@v5
      - name: Generate with _just
        uses: js-just/latest@main
        with: # Remove "with" and "path" here if you are not using compressor or generator modes!
          path: . # Root directory, or you can replace the dot with the path to your website/docs directory to be generated/compressed. (Only for compressor and generator modes)
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: . # Root directory, or you can replace the dot with the path to your entire website to be deployed to GitHub Pages.

  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
\`\`\`
You can change name of workflow file and workflow name:
\`\`\`yml
name: Workflow name
\`\`\`
You can also choose _just version:
\`\`\`yml
        uses: js-just/_just@(put version name here)
\`\`\`
**If you know what exactly you are doing, you may change anything.**
- Create `just.config.js` file in the root directory:
Choose what mode you want to use.
 
Using `Postprocessor` mode:
\`\`\`js
module.exports = {
  type: "postprocessor"
}
\`\`\`
Using `Redirector` mode: 
\`\`\`js
module.exports = {
    type: "redirect", 
    redirect_config: {
        url: "https://example.com/", // Required. Replace with destination URL.
    }
}
\`\`\`
Using `Compressor` mode:
\`\`\`js
module.exports = {
    type: "compress"
}
\`\`\`
Using `Generator` mode:
\`\`\`js
module.exports = {
    type: "docs",
    docs_config: {
        title: "Documentation title", // Required. Replace with your documentation title.
        domain: "example.com" // Required. Replace with your domain name. Domain name should be valid.
    }
}
\`\`\`
- Read the documentation for the mode that you’ve chosen.
---
### Pro installation
- Create or modify your `.github/workflows/github_pages_workflow_name.yml`:
Make sure that permissions allow writing `pages` and `id-token`, but do not allow writing `contents`, only read.
\`\`\`yml
permissions:
  contents: read
  pages: write
  id-token: write
\`\`\`
Make a job for building your website using _just:
\`\`\`yml
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4
      - name: Generate with _just
        uses: js-just/_just@ # version name (recommended) (example: v0.0.29) / main branch (latest commit) (unstable, not recommended) / commit SHA (not recommended)
        with:
          path: # Path to your website directory to be generated/compressed. (Only for compressor and generator modes)
\`\`\`
- Create `just.config.js` file:
Basic usage:
\`\`\`js
module.exports = {
  type: "(postprocessor/redirect/compress/docs)"
}
\`\`\`

- Read the documentation for the mode that you’ve chosen.

## Modes documentation
- [Postprocessor](/docs/modes/postprocessor)
- [Redirector](/docs/modes/redirector)
- [Compressor](/docs/modes/compressor)
- [Generator](/docs/modes/generator)

## Reserved directories
Your repository should not have these directories:
- _just_data
- deploy
If your repository has any of these, _just will throw an error.

_just: prev: /docs
```
## test
```md
_just: title: Compressor Mode
# Compressor mode
**- Compresses your website.**

> This mode compresses your static website's `.html`, `.js`, `.css`, `.xml`, `.svg`, `.json` and `.webmanifest` files.<br> It removes every comments, tabs and newlines. <br><br>For JavaScript it also compresses booleans and undefined: <br><ul style="margin-bottom: -19px"><li> `true` -> `!0` </li><li> `false` -> `!1` </li><li> `undefined` -> `[][[]]` </li></ul>

> [!WARNING] This mode is under development, and it may cause JavaScript and HTML errors! <br>To fix JavaScript, do not forget semicolons. <br>To fix HTML newlines, use `<br>` instead. <br>Please [report any bugs](https://github.com/js-just/_just/issues/new?labels=bug&template=bug.md) you find.
<br><br>
This mode requires only the `just.config.js` file and the workflow file.
-# `just.config.js`
\`\`\`js
module.exports = {
  type: "compress"
}
\`\`\`

-# `.github/workflows/WORKFLOW_NAME.yml`
\`\`\`yml
name: Website

on:
  push:
    branches: ["main"]
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: "pages"
  cancel-in-progress: false

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4
      - name: Setup Pages
        uses: actions/configure-pages@v5
      - name: Generate with _just
        uses: js-just/latest@main
        with:
          path: . # Root directory, or you can replace the dot with the path to your website directory to be compressed.
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: . # Root directory, or you can replace the dot with the path to your entire website to be deployed to GitHub Pages.

  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
\`\`\`

## How it works?
It compresses files by removing tabs, newlines and comments.
Also it compresses booleans and undefined in JavaScript:
\`\`\`js
!0     // true
!1     // false
[][[]] // undefined
\`\`\`
-# `[][[]]` ( `undefined` ) by [JSFuck](https://jsfuck.com/)

_just: prev: /docs/getting-started
```
```md
_just: title: Generator Mode
# Generator mode
**- Generates documentation website using Markdown.**

> This website is generated using this mode.

This mode requires the `just.config.js` file and the workflow file.
-# `just.config.js`
\`\`\`js
module.exports = {
  type: "docs",
  docs_config: {
    title: "Documentation title", // Required. Replace with your documentation title.
    domain: "example.com" // Required. Replace with your domain name. Domain name should be valid.
  }
}
\`\`\`

-# `.github/workflows/WORKFLOW_NAME.yml`
\`\`\`yml
name: Website

on:
  push:
    branches: ["main"]
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: "pages"
  cancel-in-progress: false

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4
      - name: Setup Pages
        uses: actions/configure-pages@v5
      - name: Generate with _just
        uses: js-just/latest@main
        with:
          path: . # Root directory, or you can replace the dot with the path to your docs directory to be generated.
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: . # Root directory, or you can replace the dot with the path to your entire website to be deployed to GitHub Pages.

  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
\`\`\`

After generating the documentation, this mode uses the [Compressor mode](/docs/modes/compressor) to compress the generated website.
Use Markdown ( `.md` ) files for documentation. You can also use HTML/CSS/JavaScript for custom pages, but remember that they will be compressed using the [Compressor mode](/docs/modes/compressor)!

## How it works?
It processes every Markdown file and generates HTML page for each of them.

## Customizing your documentation website
You can customize your documentation website with the `just.config.js` file.

You can make the HTML title tag and `<meta property="og:title">` differ from `title` by adding the `metatitle`:
-# `just.config.js`:
\`\`\`js
module.exports = {
  // ...
  docs_config: {
    // ...
    metatitle: 'This text will be inserted in HTML <title> and <meta property="og:title"> tags'
  }
}
\`\`\`
You can also make the `<meta property="og:title">` differ from `metatitle` by adding the `og` and `og.title`:
-# `just.config.js`:
\`\`\`js
module.exports = {
  // ...
  docs_config: {
    // ...
    og: {
      title: 'This text will be inserted in HTML <meta property="og:title"> tag'
    }
  }
}
\`\`\`

You can add a description to your documentation website:
-# `just.config.js`:
\`\`\`js
module.exports = {
  // ...
  docs_config: {
    // ...
    description: 'This text will be inserted in HTML <meta name="description"> and <meta name="og:description"> tags'
  }
}
\`\`\`
You can make the `<meta name="og:description">` differ from `description` by adding the `og` and `og.description`:
-# `just.config.js`:
\`\`\`js
module.exports = {
  // ...
  docs_config: {
    // ...
    og: {
      // ...
      description: 'This text will be inserted in HTML <meta name="og:description"> tag'
    }
  }
}
\`\`\`

You can add `<meta name="keywords">` HTML tag by adding the `keywords`:
-# `just.config.js`:
\`\`\`js
module.exports = {
  // ...
  docs_config: {
    // ...
    keywords: 'Your,website,keywords,here'
  }
}
\`\`\`

You can add footer text by adding the `footer`:
\`\`\`js
module.exports = {
  // ...
  docs_config: {
    // ...
    footer: 'This text will be footer text'
  }
}
\`\`\`

You can change the `<meta property="twitter:card">` by adding the `twitter` and the `twitter.card` in `docs_config`. `summary_large_image` by default.

You can add buttons and links to header/navbar:
-# `just.config.js`:
\`\`\`js
module.exports = {
  // ...
  docs_config: {
    // ...
    links: [
      ["a link", "https://example.com/", "_blank"] // [ " link title " , " URL " , " HTML <a> target " ]
    ],
    buttons: [
      ["a button", "https://example.com/", "_blank"] // [ " button title " , " URL " , " HTML <a> target " ]
    ]
  }
}
\`\`\`

### Icon
To add an icon to your documentation pages, you can insert your custom HTML in `<head>` by adding the `insertInHTMLHead`:
-# `just.config.js`:
\`\`\`js
module.exports = {
  // ...
  docs_config: {
    // ...
    insertInHTMLHead: '<!--   Your HTML code here   /-->'
  }
}
\`\`\`
To add an icon to header/navbar in generated documentation pages, you can specify image URL by adding the `logo`:
-# `just.config.js`:
\`\`\`js
module.exports = {
  // ...
  docs_config: {
    // ...
    logo: 'http://example.com/logo.png'
  }
}
\`\`\`

### Third-party services
Currently, Just an Ultimate Site Tool supports only adding these third-party services:
- Google Analytics
- Google Site Verification
- Yandex Site Verification
-# `just.config.js`:
\`\`\`js
module.exports = {
  // ...
  docs_config: {
    // ...
    google: 'google site verification',
    googleAnalytics: 'google analytics', // example: 'G-..........'
    yandex: 'yandex site verification'
  }
}
\`\`\`

_just: prev: /docs/getting-started
_just: next: /docs/generator/syntax
```
```md
_just: title: Postprocessor Mode
# Postprocessor mode
**- Add your own files to generated Next.js website.**

> With this mode you can add your own files to generated Next.js website. <br>This mode creates the `deploy` directory and outputs files into it.<br> If your Next.js website outputs an `en.html` file, it will be copied into the `index.html` file.

This mode requires the `just.config.js` file, the workflow file, some directories and `_just/404.html`.
Next.js website should be generated **before** running this mode.
Generated Next.js website should be in `.next/server/pages/` and `.next/static/` directories.
Required directories are:
- `_just`
- `_just/dangerously-insert-files`
- `_just/js`
- `_just/style`

-# `just.config.js`
\`\`\`js
module.exports = {
  type: "postprocessor"
}
\`\`\`
-# `.github/workflows/WORKFLOW_NAME.yml`
\`\`\`yaml
name: Website

on:
  push:
    branches: ["main"]
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: "pages"
  cancel-in-progress: false

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4
      - name: npm
        id: install-npm
        run: npm i
      - name: Detect package manager
        id: detect-package-manager
        run: |
          if [ -f "${{ github.workspace }}/yarn.lock" ]; then
            echo "manager=yarn" >> $GITHUB_OUTPUT
            echo "command=install" >> $GITHUB_OUTPUT
            echo "runner=yarn" >> $GITHUB_OUTPUT
            exit 0
          elif [ -f "${{ github.workspace }}/package.json" ]; then
            echo "manager=npm" >> $GITHUB_OUTPUT
            echo "command=ci" >> $GITHUB_OUTPUT
            echo "runner=npx --no-install" >> $GITHUB_OUTPUT
            exit 0
          else
            echo "Unable to determine package manager"
            exit 1
          fi
      - name: Setup Node
        uses: actions/setup-node@v4
        with:
          node-version: "20"
          cache: ${{ steps.detect-package-manager.outputs.manager }}
      - name: Setup Pages
        uses: actions/configure-pages@v5
        with:
          static_site_generator: next
      - name: Restore cache
        uses: actions/cache@v4
        with:
          path: |
            .next/cache
          key: ${{ runner.os }}-nextjs-${{ hashFiles('**/package-lock.json', '**/yarn.lock') }}-${{ hashFiles('**.[jt]s', '**.[jt]sx') }}
          restore-keys: |
            ${{ runner.os }}-nextjs-${{ hashFiles('**/package-lock.json', '**/yarn.lock') }}-
      - name: Install dependencies
        run: ${{ steps.detect-package-manager.outputs.manager }} ${{ steps.detect-package-manager.outputs.command }}
      - name: Build with Next.js
        run: ${{ steps.detect-package-manager.outputs.runner }} next build
      - name: Override with _just
        uses: js-just/latest@main
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: deploy

  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
\`\`\`

## Required directories
-# Required only directories, not files within them (except for the `_just/404.html` file).

In **`_just/js`** you can put your JavaScript files to be inserted into the `deploy/_just` directory and into every HTML page.
In **`_just/style`** you can place your CSS files to be inserted into the `deploy/_just` directory and into every HTML page as well.
Within **`_just/dangerously-insert-files`**, you may include any files that you wish to insert into the `deploy` directory.

> [!CAUTION] Inserting files via the **`_just/dangerously-insert-files`** directory may cause website errors or files may not be inserted if there is already a file with the same name in the `deploy` directory.

The **`_just/404.html` file** is required and it’ll become the `deploy/404.html` file (if the `deploy/404.html` file already exists, it will be overwritten).

## How it works?
1. It creates a `deploy` directory and copies every file from the `.next/server/pages` and `.next/static` directories to it.
2. If there is a `deploy/en.html` file, it will copies to the `deploy/index.html`.
3. It copies `_just/404.html` to `deploy/404.html` (if it exists, it’ll be overwritten).
4. Every `_just/js/*.js` copies into `deploy/_just`; Every `_just/style/*.css` copies into `deploy/_just`; Every `_just/dangerously-insert-files/*` copies into `deploy`.
5. Every JavaScript and CSS file in `deploy/_just` are inserted into every HTML page as HTML tags.

## Troubleshooting
This mode may cause website issues.
You can try fixing them by switching the postprocessor version in your workflow file.
\`\`\`yaml
      - name: Override with _just
        uses: js-just/latest@main
        with:
          postprocessor-version: "26"
\`\`\`
Available postprocessor versions are: `"24"`, `"26"` (default), `"32"`.

---

-# You can support Just an Ultimate Site Tool by setting the `watermark` to `true` in the `module.exports` of the `just.config.js` file. This will add two comments about the Just an Ultimate Site Tool to every HTML file. Thank you.

_just: prev: /docs/getting-started
```
```md
_just: title: Redirector Mode
# Redirector mode
**- Client-side redirect.**

> This mode redirects your static website, such as your `.github.io` website, to a specified URL. <br>This mode creates the `deploy` directory and outputs files into it.

This mode requires only the `just.config.js` file, (except for the workflow file).
`just.config.js`
\`\`\`js
module.exports = {
  type: "redirect", 
  redirect_config: {
    url: "https://example.com/" // Required. Replace with destination URL.
  }
}
\`\`\`
> [!TIP] Do not use this mode if you can make server-side `HTTP 3XX` redirects.

You can add `params{}`:
\`\`\`js
module.exports = {
  type: "redirect", 
  redirect_config: {
    url: "https://example.com/", // Required. Replace with destination URL.
    params: { // Optional.
      title: "redirect website title here", // Optional. Replace with any title you want. Recommended.
      description: "redirect website description here", // Optional. Replace with any description you want.
      keywords: "some, keywords, here", // Optional. Replace with any keywords you want. Separate keywords by commas.
      htmlLang: "en", // Optional. <html lang="${htmlLang}">
      robots: "index", // Optional. <meta name="robots" content="${robots}">
      charset: "utf-8", // Optional. "utf-8" by default. <meta charset="${charset}"> and file charset.
      viewport: "width=device-width, initial-scale=1.0", // Optional. "width=device-width, initial-scale=1.0" by default. <meta name="viewport" content="${viewport}">
      yandex: "", // Optional. Put your Yandex verification string here. <meta name="yandex-verification" content="${yandex}">
      google: "", // Optional. Put your Google verification string here. <meta name="google-site-verification" content="${google}">
      googleAnalytics: "" // Optional. Put your Google Analytics ID here.
    }
  }
}
\`\`\`
You can also add `content{}` in `params{}` if you want to modify HTML content.
\`\`\`js
module.exports = {
  type: "redirect", 
  redirect_config: {
    url: "https://example.com/", // Required. Replace with destination URL.
    params: { // Optional.
      content: { // Optional.
        text1: "Redirecting...", // Optional. "Redirecting...<br>" + generated content ("<small>to <a ...>...</a></small>") by default.
        text2: "Didn’t get redirected?", // Optional. "Didn’t get redirected?" by default.
        text3: "Click here!" // Optional. "Click here!" by default. <a ...>${text3}</a>
      }
    }
  }
}
\`\`\`
> [!NOTE] `\\n` and tabs/4 spaces will be removed. Use `<br>` instead of `\\n`.

Remember that your repository should have a `.github/workflows/WORKFLOW_NAME.yml` file.
-# Template:
\`\`\`yml
name: Website

on:
  push:
    branches: ["main"]
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: "pages"
  cancel-in-progress: false

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4
      - name: Setup Pages
        uses: actions/configure-pages@v5
      - name: Generate with _just
        uses: js-just/latest@main
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: deploy

  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
\`\`\`

### Redirect paths
You may add `paths[]` in `redirect_config{}` to create custom redirect paths.
\`\`\`js
module.exports = {
  type: "redirect", 
  redirect_config: {
    url: "https://example.com/", // Required. Replace with destination URL.
    paths: [ // Optional
      {
        path_: "example", // Required. Replace with path.
        url: "https://example.com/", // Required. Replace with path destination URL.
        params: { // Optional.
          title: "redirect website title here", // Optional. Replace with any title you want. Recommended.
          description: "redirect website description here", // Optional. Replace with any description you want.
          keywords: "some, keywords, here", // Optional. Replace with any keywords you want. Separate keywords by commas.
          htmlLang: "en", // Optional. <html lang="${htmlLang}">
          robots: "index", // Optional. <meta name="robots" content="${robots}">
          charset: "utf-8", // Optional. "utf-8" by default. <meta charset="${charset}"> and file charset.
          viewport: "width=device-width, initial-scale=1.0", // Optional. "width=device-width, initial-scale=1.0" by default. <meta name="viewport" content="${viewport}">
          yandex: "", // Optional. Put your Yandex verification string here. <meta name="yandex-verification" content="${yandex}">
          google: "", // Optional. Put your Google verification string here. <meta name="google-site-verification" content="${google}">
          googleAnalytics: "" // Optional. Put your Google Analytics ID here.
        }
      }
    ]
  }
}
\`\`\`

## How it works?
It generates HTML pages based on your `module.exports` input.
Every generated HTML page has:
- `<meta http-equiv="refresh" content="0;url=...">` in `<head>`. This means that the user will be redirected to the destination URL in 0 seconds after the page has loaded.
- Fallback #1 - `<script>...</script>` in `<body>` redirects the user to the destination URL.
- Fallback #2 - Other elements in `<body>` ("Redirecting... <...>", "Didn’t get redirected? `<a ...>` Click here! `</a>` ").
That means that users should be redirected, even if they have disabled JavaScript in their browser settings.

## Why is `HTTP 3XX` better?
A response with an `HTTP 3XX` status code and with the `location` header makes a real redirect.
This mode generates client-side redirects that only support browsers!

---

## `module.exports` JSON Schema
\`\`\`json
{
  "$id": "https://just.is-a.dev/schema/r.json",
  "$schema": "http://json-schema.org/draft-04/schema#",
  "description": "_just just.config.js module.exports Redirector mode",
  "type": "object",
  "properties": {
    "type": {
      "type": "string"
    },
    "redirect_config": {
      "type": "object",
      "properties": {
        "url": {
          "type": "string"
        },
        "params": {
          "type": "object",
          "properties": {
            "title": {
              "type": "string"
            },
            "description": {
              "type": "string"
            },
            "keywords": {
              "type": "string"
            },
            "htmlLang": {
              "type": "string"
            },
            "robots": {
              "type": "string"
            },
            "charset": {
              "type": "string"
            },
            "viewport": {
              "type": "string"
            },
            "yandex": {
              "type": "string"
            },
            "google": {
              "type": "string"
            },
            "googleAnalytics": {
              "type": "string"
            },
            "content": {
              "type": "object",
              "properties": {
                "text1": {
                  "type": "string"
                },
                "text2": {
                  "type": "string"
                },
                "text3": {
                  "type": "string"
                }
              },
              "required": []
            },
            "og": {
              "type": "object",
              "properties": {
                "title": {
                  "type": "string"
                },
                "description": {
                  "type": "string"
                }
              },
              "required": []
            },
            "twitter": {
              "type": "object",
              "properties": {
                "card": {
                  "type": "string"
                }
              },
              "required": [
                "card"
              ]
            }
          },
          "required": []
        },
        "paths": {
          "type": "array",
          "items": [
            {
              "type": "object",
              "properties": {
                "path_": {
                  "type": "string"
                },
                "url": {
                  "type": "string"
                },
                "params": {
                  "type": "object",
                  "properties": {
                    "title": {
                      "type": "string"
                    },
                    "description": {
                      "type": "string"
                    },
                    "keywords": {
                      "type": "string"
                    },
                    "htmlLang": {
                      "type": "string"
                    },
                    "og": {
                      "type": "object",
                      "properties": {
                        "title": {
                          "type": "string"
                        },
                        "description": {
                          "type": "string"
                        }
                      },
                      "required": []
                    },
                    "twitter": {
                      "type": "object",
                      "properties": {
                        "card": {
                          "type": "string"
                        }
                      },
                      "required": [
                        "card"
                      ]
                    }
                  },
                  "required": []
                }
              },
              "required": [
                  "path_",
                  "url"
              ]
            }
          ]
        }
      },
      "required": [
        "url"
      ]
    }
  },
  "required": [
    "type",
    "redirect_config"
  ]
}
\`\`\`

_just: prev: /docs/getting-started
```
```md
_just: title: Docs
# _just Docs
Welcome to Just an Ultimate Site Tool documentation!
-# "**`_just`**" is an abbreviation of **Just an Ultimate Site Tool**.
---
## What is _just?
Just an Ultimate Site Tool is a GitHub Action for building static websites. 
Currently it have 4 modes: 
- `Postprocessor`: Add your own files to generated Next.js website.
- `Redirector`: Client-side redirect using JavaScript.
- `Compressor`: Compresses your website.
- `Generator`: Generates documentation website using Markdown.

> [!WARNING] Just an Ultimate Site Tool is still in development at the **alpha** stage. Expect regular updates, possible bugs, and changes. If you have found a bug, please [report it here](https://github.com/js-just/_just/issues/new?labels=bug&template=bug.md).
> [!NOTE] Just an Ultimate Site Tool assumes that a modern browser and a modern operating system are used.
> [!TIP] Do not use `Redirector` if you can make server-side `HTTP 3XX` redirects.

## Why _just?
1. __No packages.__
2. __Fast build.__
3. __**No watermarks.**__
-# _just uses Node.js, but _just does not require you to use Node.js/npm/pnpm/Yarn/related packages stuff.

_just: next: /docs/getting-started
```
```ico
         h  6          (  �  00     h&  �  (                                                                                                                                                                                                                                                         �=lP�<l��<l��<l��:lm�<k&�:lm�;ky�;kE� �                    �;k��<l��<l��<l��<l��<l��<l��<l��<l��<l��<k��7m            �9iP�<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��=lG            �;l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��;k��3f
            �<l��<l��<l��<l��<l��<l��<l��<l��<l��<l�                        �<l��<l��<l��<l��<l��<l��<l��<l��<l��;l�            �.]�=l��;k��<l��<l��<l��<l��<l��<l��<l��<l��<l��;l�            �=lG�<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��:kO            �7m�<k��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l�                    � ��;kE�;ky�=lm�;i'�=lm�<l��<l��<l��9iP                                                                                                                                                                                                                                                                                    (       @                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          �@`�;j[�;l��=l��<l��<l��<ms�7d                                                                                            �=mT�;k��<l��<l��<l��<l��<l��<l��<l��<l��<k&�;lt�;l��;k��<l��<l��<l��:j`�@`                                            �  �;l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��;jR                                        �;k��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��;i8                                �:lS�<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<k�                            �9U	�;k��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<k�                            �:l\�<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��;k��=m*                            �<k��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<k��;l��;l4                                �<k��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��                                              �<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l�                                                �<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l�                                            � ��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��;l�                                �;l4�<l��<k��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<k�                            �;k+�<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��:l\                            �;l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��Im                            �=l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��;jR                                �>n:�;l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l�                                        �;mR�;k��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<k�                                                �@`�:m`�<l��;k��<l��<k��<l��;ku�<k&�<l��<l��<l��<l��<l��<l��<l��<l��;k��:lS                                                                                            �>j�:lv�<l��<l��;k��;k��:i\�@`                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            (   0   `                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  �3f�;l�;j<�=ku�<m��;l��<l��<l��=lP�@j$�7m�                                                                                                                                          � ��;k+�:k��<k��<l��<l��<l��<l��<l��<l��<k��<l��<k��<m^�<i        �;b�;j<�:m`�;lt�=mz�=lv�;kd�<l@�6k                                                                                        �;j[�<l��<k��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��=k��=nC�;mg�<l��<l��;l��<l��<l��<l��<l��<l��<l��<k��:h                                                                        �7m�<k��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��;lq�6k                                                            �7m�;l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��;l��9c                                                        �=k��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<k��:k�                                                � ��:kX�<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��;l��;b                                            �=m*�<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<k��=f                                        �3f�<k��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<k��@`                                        �;l�<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��;jl                                            �;nA�<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��Im                                            �<j��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��;l��;k��<k��;n<�@`                                                �;k��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<m��UU                                                                    �;l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<k�                                                                        �<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l�                                                                        �<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l�                                                                        �<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l�                                                                    �@��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l�                                                �9U	�:k>�<m��<k��<l��<m��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<k�                                            �Im�;l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��;l��;nA                                            �;ml�<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<k��;l                                        �@`�;l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��@�                                        �9h�<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<k��8j)                                            �3f�<k��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<k��;lW� �                                                �<m��<k��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��=k�                                                        �@f�;l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��;k��7m                                                            �6k�:kr�<k��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��;b                                                                        �7o�<m��<m��<l��<l��<l��<l��<l��<l��<l��<l��;lh�;kE�=k��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<l��<jY                                                                                        �6k�<l@�;kd�:lv�;l}�<kw�:j`�>j:�@j        �@p�;k_�<m��;l��<l��<l��<l��<l��<l��<l��<l��<k��;l��=m*� �                                                                                                                                        �  �3f�<k&�<kQ�;l��;k��<l��<k��:lv�;j<�;l�@�                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    
```
```html
<!DOCTYPE html>
<html>
    <head>
        <link rel="preconnect" href="https://fonts.googleapis.com">
        <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
        <link href="https://fonts.googleapis.com/css2?family=Lexend+Zetta:wght@100..900&family=Rubik+Mono+One&family=Rubik:ital,wght@0,300..900;1,300..900&family=Source+Code+Pro:ital,wght@0,200..900;1,200..900&display=swap" rel="stylesheet">
        <link href="/css/styles.css" rel="stylesheet">
        <title>Just an Ultimate Site Tool</title>
        <link rel="apple-touch-icon" sizes="180x180" href="/img/apple-touch-icon.png">
        <link rel="icon" type="image/png" sizes="32x32" href="/img/favicon-32x32.png">
        <link rel="icon" type="image/png" sizes="16x16" href="/img/favicon-16x16.png">
        <link rel="manifest" href="/site.webmanifest">
    </head>
    <body class="bgb xh h">
        <div data-just="navbar"></div>
        <script src="/_just/static/navbar.js"></script>

        <h1 class="bg lz cw beta agt t z" style="position:relative" id="h">Just an Ultimate Site Tool</h1><h1 class="bg lz cw beta agt t b" style="position:absolute">Beta</h1>
        <script>document.getElementById('h').style.translate='0px 0px'</script>

        <h2 class="lz u0">A GitHub action to <span class="lz">enhance</span> your static website.</h2>

        <div class="p u1">
            <img class="l" src="/img/logo.svg" height="100" width="100">
            <img class="top" src="/img/line.svg" style="display:none">
            <img class="btm" src="/img/line.svg" style="display:none">
            <div class="c"></div>
            <div class="r"></div>
            <div class="tl" style="display:none"></div>
            <div class="bl" style="display:none"></div>
            <div class="d"></div>
            <span></span>
        </div>
        <div class="pjs"></div>
        <script>document.querySelector('.p').style.width='100%'</script>

        <div class="btns u2">
            <a href="/docs/getting-started" target="_self" class="bg">Get Started</a>
            <a href="https://github.com/js-just/_just" target="_blank" class="gh">Repository</a>
        </div>

        <strong class="u3">Just an Ultimate Site Tool is <a href="https://github.com/js-just/_just" target="_blank">open-source</a>.</strong>
        <span>Licensed under the MIT License</span>

        <small class="copy"><a href="https://github.com/js-just/_just/blob/main/LICENSE" target="_blank">Copyright &copy; 2025 &#171;JustStudio.&#187;</a></small>

        <script src="/js/p.js" defer></script>
    </body>
</html>

```
### test
```png
�PNG

   IHDR  &   �   ���;   IDATx�y��E��g��]X�@Qlq#�
-
YA����1AM�T��$".��B#� "���lZ�-P�Mi�.�A~�L;����s�������;����|�����9�nBx�$��|��=�{����~��$ 	H@�'`�#%�0)1�% 	H@h�I��ڰ$ ��0"	���¤Մm_��$ ��	(L�F��$P>F$	t�I����H@�����¤����G��$ 	�:�I����K@��JD@aR��,_(F$	H@�F@a�o#n���$ �.&�0i��ؕ$ 	H@CP���R	H@���H�a�F/�J��$ �� �0�a6H	H@�9}�¤Oް% 	H@�H@aҍ��O���G��$P�I]�4��$ 	H�&��l�@��$�
��`�Q	H@��FC@a2j֑@��$ �� �0�a�		H@�� �0���|�H��z��¤'�M�% 	H@�$�0�q�K	H@�@_P���0�$ 	H�7tF�����$ 	H��&mnw��$ �V���&�<z�.	H@(�I��p$ 	�����I?���J@�����¤�H�$ ��0"	H�6�Im6�H@��$�f
�6�;	���I@h�I�Xڒ$ 	H@P�4���#`D��$�9
�α�g	H@��*(L*���ш$ 	H@�C@a�;c����$ ���9aR�1@	H@�@P�����$ 	H��@�&��$ 	H P�d^% 	H�|���(Lzn�tX��$P^
�򎭑I@�#`D(=�I�� % 	H@�C@a�;c��(#��$PA@aR�G	H@��:G@a�9��\>F$	H@P�4����$ 	4��¤y,�גI@���L@a�f�v'	H@�@m�$LjS�D��$ �� �0�a�		H@�@�h��
��p�	H@���@@a��6!	H@�#`D�!�0�w{��$ 	H�
�I(fI@(#�@oP���8�$ 	H�/(L�b�R�#`D�@9	(L�9�F%	H@�I
��6�.#��$ (L�`��$ 	H�+(L�b��I@��FC@a2j֑�$ 	H�%&ua�H��$ �vP����}H@��$P�@�DaR��$ 	H@�%�0�,{��$ ��0�(L�gU	H@���K@a�\��&	H�|�Hm$�0i#l���$ 	H`h
���X*	���I@]L@a�Ń�k��$ �~#�0�7��0"	H@%"�0)�`�$ 	H��	(Lz}��I@�@P�����$ 	H��(LZ="�/	H@�@�&u��P��$ �V�0i�?�/	H@�@P�����$ 	t�Q����$ 	H�k(L�f(tD�@��FJ@a2Rb�K@��$�2
����a	H�|�Hh5�I�	۾$ 	H@uP�ԍJC	t����^{-�뮻nD��V֝6mZX�bEjwÆaƌ#j��ĐY̛7o�.+17��Q;aE	H (L|H@��$�5&]3:�8[��$ �^'�0���	H@�@�(L�x0uM��$�o&�6��+	T%�nݺ��/V-3Sh�6
��eO�@��m�Gy$<�����:�����������N:餦tt�QG�[n�%�������A,<��#�v�~��~򓟄U�V�W^y%�E�˖-W_}u3fLU�����fJ@m#�0ij;�@�8��3_�}׻�v�u�@�1�;6s�1a�ܹa���)�/���7��w�>�я�׿��a�vHM�øq��ԩS�7��뮀�H�5^v�m�p����3g�}��7l���ɒ68��p�E%A5y��_|A��UᜇP��^%�J�¤$ih6VN;�����a�ڵ��;�s��	?�px饗Rw��K�ԧ>�DE���7�����}.	�����}��?�E, ,N>���ӟ����Yy9�C��/��x�p��7'�r�ʴrco{���W\����
�c�3׬Y�o�J@m$�0i#l��@�x��Wï��p�A�SN9%�{��裏���gºu�R8��{8���H^X!��'?v�y�$}��0}���>���"F1����_>d7l�\x���xG���?�8�𖷼%�s�=��c8���ñ�;d;v���)�I��aK���|��7nd���(p���c8����Q���>��c�Tg�ҥ����O�%)c��"2n���$*ؚ��?X�j2���˕W^~��ʧNSlM�8q����?��λ�_$ ��P����I�w�J�y&�j^/^�8.�l�}��Rw��
[*T������-Z�cՄ��[+�������\ �
���?���_�h�wx`�/�p�7?�s^CW+K@uP�ԅI#	��M�6�%K���� y�f%��a�΁�D��?�8�5�w��j[?�VhX�%�������:�1��9'|�s-&�M�@{	(L����$PŁ��b����w@P��3�<�Lb��oxC�o�K^�a��駟nv�'	�A@aR$M$��^���u�{U}�|�)U���k�u�i"	� �I�.��@�5�z�����k����n�M�6��\�*�a���5��� �J���+V����X��/~�Sl�t�B	��I�!�� ��0h���H^a�����p��]���Ud����~������_��mY>3�|�d�mYO���u{-�+9��AN~��sΩ+b~��NØC���_�m{�P�s�=��e[�#�H��^�:�0aT��y��'�}�_8�S�۶=	H�>
��8i%��"��C�<��?>�[#Yp�r�m~=u��������k��.��[�Ϗ����N]𫮧�zj��3��`�ˇ?�ᐿ��*����͹��磌�J?��������% ��l�j8�N�ۿ$0��o��0����c1����w_�?������8tJClS��W��c�?���Ϧ�'M�~���m��i��믿>�~��!�X��\��T�	H�t&�R�_���ÂR�1��v�o~��wm��oŰ*�~�����s�Q��}�ǎ�_���_�ek&���5D�A7�tSZ�yꩧ�j���l�c�=���o��gV�8TK?�y�浤�@7�&�&�4�"���R�Yw&P�U�m�=��3v�a�ս�Mo
.�1R֯_�����??=w���_�r'�'|�u�]ӪɌ3?=�7�b��f-lY7�&	H��&�W��L��}�{��g����.��Z��
����VVN~����S���|�+]E�?����/����l�� �#�|��p��$^�L����L@a�ˣ���J`�ܹ�3�H�����;��1��AW��r衇�Y�f��h���q����1�0v��0gΜ͹�����K�N;���2eJu�:s93��_�0l�1�>~����w�{H?fΜ�|�1�O<1�?��1&��h[�=���*��^h��ה-I@�@+ئ���¤�F�X% 	H@]N@a���{(#��$P��¤6K$ 	H@h3�I���]��$ 	4��¤y,mI��$ �	(LX��F$	H@��a�	?rdz-�M9�������|��=0�0�1�xQ��w�>�(�e�b�A�2�Q1v/�a�I�s�Y��$ �.'�t�&MGj���$ 	����d��'	H@�#`D'�0���$ 	H@���$��*	H�|�H=G@a�sC����$ ��P��wl�L�#`D�@�	(LJ?��.��G����f4ݴ6{�p�e���f̘6l���2l�M�J���,,Ñ@�	(LJ?���iӦ�+V�q�ƅ�ӧ	��   IDAT����ٳ��y�i�iu�v؈ڛ9sf;vl�3gΈ���1�v���w޹��G	H�@@aR��*	��i��½��;P|饗���?y�I�x#	H@P�4��ʃ���o|c�i���Fv�'&�R\q`��w4xΕYaa����oO�,َm�\��X����-�iӦ��$�O��/�0���K.	<�O�g�}��|}�}�����<�V���Q�}N�],��'S�J;��%��3���<�������}p�W�I�K�js�m��&L�&O���e9�<���2_I�P�cŧ�O��C�{�C����rRe?��e$ʱ�Z�;��N@aR�6��̟??09TN��e�:��c�<1�t噉�h�I'�k��&m�s�=i�dR*�1��	���_�Vhb�iK��%&;&u���_^~���'Ϲ�b_�0Q�W.��N�:5�}���7��{�×���3b�Ｍ�~��p�A�W�<���)�CIW��>b/�! �RZ�lY����?�-�A~2��R+6��駟V�\-Z��x�zW�'Ϥ}��7����}]�z5�)��ŋ�)S��3=�\�|yʋ1��A+i�8~��d�5k���胆i�s���XPf�@�P��qK$��_̚5+lܸ1	>͒*?#&M������j�=K�.�a�č����GY� �<���D��o}�촥����s�Iy�^���|��i�>��k�����'��z衁	����@�y[�+�Hl����,Y���${��[��{9��;.� ���+��<�Oy2��Be�oC=�a���1�,h��%�y�ĉ�,�����/��x㍛�����ӟ����ͯ0�|	�w�����a��c���'�|r�9� Q�*�"�>#�0�7ܑ��+��c��UZ3fL�馛+<�IvժU<$&5V��KA�87�t^-����0|JgB�e��w_+y����Z�V���o9/_����V�s�5��A��'eQ��gl�/�a�S~Ϋ,�46��K��z�0�]�ʂBb�K�s=�5<S����y$� ��y�z<��R�C=T4�^%$P=$�Iu.�J�*>��Ӓ<�hY%a%cFq�&�Y��Vjrb��f���v�`W�,D�<&u��}�ger�\�JC�d�f\;N�"䥗^
p��"�/E�Q�_⩖o�$0:
��q��­�ޚ(�
����/&K�JJ���{&����w-�'���N������w���"ȃ+$���olm�1�/V��VG�m�E$�A�<�BLU�bT돺��+WR�٘W.F�
��p���`�&2�bybe�+��I��<�q?�Ķ� ��+�shr$�5b���~Q|�8��[!�z�)�$�����*e�QX�#qbG�5#�-�w�j3��t�1�/� �k��J
⃺l�d|�I�+)����m9V�r�^%�O&�4��:b���������|�,��IeR�[;\�v�#�<��n�@L~6e˨���/|��k�������#��M���a���j�ϖ�ha���,
��s�@yԥ�9r��g�F8�v��*	�6�!���X�Z���&���\+�'�ay���!�7�I�8�ڟ�Q�!�0�w{�L�����/�r֡�����h�{&U�C`Ǖ����|���+���~��o��?�O���*y�HĈak	?胳6\�JyKfَ�m�JL·W�#��%�g��y�ϓ{��+�B���P^�W�%ƆsG�����ذ���W�S-�6�g�h!v�w2%V��*�A%#�0)ـN�	0a���~��k�q��dR��g��m�x8O�M���m1ٱ�Q-/�-m�~�v�'/����6�S����\V�li�r�I�Ÿ��&�D�Ƞ�V��c��m�8�7��+e1n�#�x�e���Ŷ�?�-�QcL?�G9�x%���z\����}�?1n�ҦZ=ڦ_��m��6�o�]wM�b��`�-�\y6I���+k`�%	4� �|��[�wT��\���^�'�V+!ŕ.��ݖ�J��-i!�rP��c����?`>ѳ����#PHL�l3�)~�����5�\8GS�h��F�5%�]&�5z#��&��D�[�b�Cn�tu ]������]��H�-�kK/v"�aX,	H@���
&	H@�����¤%�`���$ 	����d4Ԭ#	H@�@K�%LZҳ�J@��$ �

�
 >J@���L��
&�J@��$�Y
���w	H@�#`Dh��¤xV�Mx`��g?�������/~1���oo(�Y�f��^e#�K���zje��36�����	'����l������]2�j>�G_4�߰�v����Ā��{�G�h_��8�������C>y$ƌ�&�r���	��2�|��Q��'�&	��I���q&����}(lڴ)�&�F�>����I�������k?��,GN=������ߞ�#GuTx�'7ޘ~�6��c�9&�^�=E9v�*c�/�R�Xv�ǆ�kצ�"eؒ����c����y&	��I?��1x�[ߚ&��ϙL8��<�I�O�L0|��|z-~�e��Smn'_?�Q���9;P��e�A_����χ�v�-0��We�Jԣ>�}�3�"��������:+��dJ�x�A�0���|�U���$����J=X���}��/X�7���z��<�_���G>W���'� �p�˰'�<�k%�"-Z�(�����W_�����O?�D�^{��A�^r��y̘1ɖ:��w�$�Ee����g�`o�@�P���Hg"�����?%�?�)c��
� �Ƀ�р�`��j:�Q����.]���$D1�2���ɓ�G�w�ygx���/������:�i(��'L��Ν�>�?�쳁>h���?�?ws٣�>:�/�IL�O=�TX�fM�'�ä�Ϭ4�2@>��U����&Z��,'8rO$�w�e.����h�?�xZ��B�z�=�\�������O��C9����Ɠ8���z��\m�'NL�˖-KW|~�W�{��u��%���sN��~���ܼu'l)'b?~<���So
�/_v�}�$|x6I�(L�a��q��7z.>�	�O�Lt��I�I%OP���$� arY�`��$F=&&k��4L�H�P>",.���4a�n1V&��+W������i��f�T�g��=1Kn�r�e�}&j�ƞ����D&^�;������<�q<XY:���)Hĝ��@f����P%���#��WL�ê�G2kb�=T��v��ö����A��_��y(#�IG��bq�L6�*Q�D�˙*�y�+�P��l���k�V�P>扑U��C�P~aS+�3���m|+�Ä�aBG��j�қ��洕TNL�s�y�8R�����J�=���L��S��N�4�����9��}�+�^}����	B�Uƛ���<D	��,�r�W	��I��x��˧r&=&�"
&eQ��)�(T�u*'a�Ꙅ�k$�c>8ɶ6��ń9���H�.��S�U��}C��_|1��D_y��l�v������1��y<��YC��l���/��T)D�a��A���q�u�f���ﱥ��Ĕ�F� �X�a�	����¤�F_ZJ�m&�)䎘�j`e���/�-`�'P��֡�	&���LB�L�4)��m5�l?��p>�6�1 �g�9���"4�2N�d�qW���mܸqi�?���� 0غ�5u�ì��L����d��Yl�'1N��}e"�8.C�[���+N���!�Uڥ�-��3�3��J�`K�ǖ���Zg�#D[��z&	���¤�#l|�0Aq0�ɎO�$&�����$�r:�eL.lP�O�LL�w\�&����p����;�$(L�)���d�}��Ve()#6���L����ץ9���3�Tk>�I�m��R�`k&+���M�&ok��舉  
IDAT��C|pO��:�IC&f��9|���2��ʰ��v8D�-�L�!�ń `���o�Q�_bŦX�{�qE�r�_��������}��Θ����:ԥ�Bh��#!p���(��C�<�$�Z-L���1���� � i���Q����<��*`�elU������\!���b�����~70A�x�;��}N�E�$�s>Wl��}-�C���-���}�F.���m感�`�}�>��g[�%q��+�����ǫ��js������?���\F��i�|����R�5��L<�ن�苔Ws�/��y$���&U��-���h�:9���r�VYh[����¤_F�8% �Q@Q�m��Jl7��.�I���j?��[aR�15"	H��X����M�l�}�t�	���¤���J���&`�&�$ 	H@�
��
���G��$ ��P�������$ 	���¤ehmX�#`D��ZM@a�j¶/	H@�@�&u�Ұ|�H�����¤�FD$ 	H@}L@aR��7	H@�@�P����$ 	H�D�X�����H@��$P�I]�4��$ 	��@���0�ҁ�-	H@�@?P����$ ��0��P��d C��$P
�2��1H@�#`D�S
�>xÖ�$ 	t#�I7��>I�|�H�@]&ua�H��$ �vP����}���I@�@K(LZ��F% 	H@��h����I@�����¤+�A'$ 	H@�@9�	��$ 	H@�9
��2��$ 	t�@+{W����mK@��$0"
���X���G�����¤�FC_$ 	H@}N@a��o ×��G��$��&�<z�.	H@(�I��p$P>F$	��I?���J@�����¤�H��G��$ 	H�6�Im6�H@��$�f
�6/_wF$	H@h�I�Xڒ$ 	H@P�T �Q��$ ��P�t��=K@�������*L�E��$ 	H@�"�0ii���$ ��0��P�4�J@��$0Z
�ђ��$ ��0"	t��¤�C���$ 	d
�L«$P>F$	��I��K@���K@aRޱ5��0"	H@�'�0)��$ 	H�w(Lzg���I@��*(L*��(	H@�@�(L��ޖ$ 	H@h��¤A�V��$ 	H�yj���aK��$ 	H�.
��0i$	H@h.[�N@aR�����$ 	t��¤��R�@���C@a���"	H@�@(L� �&$ ��0"	H�3&��n���$ 	T!�0��,	���I@�
��'���$ 	��I_s��4"	H@('�I9�ը$ 	H@=I@a�æ��$ 	@@a�$ 	H@]A�%¤+"�		H@��z��¤�L�% 	H��	�:|�I����$ 	H@�E@a�[㥷���G��$P �0)��V��$ ���0�6mZX�bE�7o^G<���^{-���ǉ��/6�I����iV	H@��'	4@`�¤���^v�eaÆ��@f7�y�a���)qO�PU�'O�$ D���p��믿>�}��aƌCֱP��$ ���0���{�~��N<���{3L�S�NM�����^ziX�|y �Z5��g�}�	k׮�f2�w��G���̙n�����q�7P�����K@h#���V�L�\�9�V x���iӦMi���.�(�3&\|��)ۜX� �g��}��a���q��������|�;�#�H��ʲ�L<�ƍ�W��Y�:q��t�E��$ ���0��5�i����\b��Ո.� �\�2\s�5a�ƍ��+�L+�6z���
��gEa�*	�EQ������_�j1k��e˖�rD�'=�RF�$	H@]L�e�w�i�p�grfΜ�����SF������RX�8��C��w�=h�$�"	H@�@��L��b1k֬���V��ES�$�y����	&$�,Vx(n��)k$��[;���ֺv&	H@(��	!2Ǝb�iKgҤI�0��|����ճ�:+�)��m�T�ö��
N�����So;�I@��$�8���ɒ%K�����r_<��3��Y�j��R��<	VO:�p뭷�<^,�?�m��Q�H�\_=�\�9�S�U�Fڶ�$ 	H@�hH���������[5'�pB:�J�|�����e��KR�M7��x�t��H��o�`_L�b,^�8�Y�&`��h���G�<��#Xx*U�r�����^�؆j�2	H@�@��~��k�E~�$Ƙ�k>��t����v�Ƹ�,�8��'~%Ƙ�b[�hl�e�Ǹ�M�sBb��N�'�q�>�g;eʔC��}�=�$ 	H@�#0ja����-K@�@}�*�m�	[!��`k�Z���~����H��$��&����Gbܲ]�����o��	[']�NH@(���&"��$ 	H@�"�0�y��@0D	H@#%�0)1�% 	H@h�I���p��$ 	����  ���d�   IDAT ��(L�RR    IEND�B`�
```
```png
�PNG

   IHDR   �   �   R�l   IDATx�|e�ǟgf�&�����`�p�=8+b�IRz�� �BK�Mi�+�-���BkK7�B�V��U�4$���U�?�G�J��4��I�;�����Mg��fgf����οw�y����f����Yd%L@P/�� ��4@I�8/�:P�JX %]���>"�} dV�D �Y���>"�} dV�D �Y���>"�} Jj&��' ؏BJ���K]|�O@��,�"@)������`?
Y(C}%"�%E@PR�-�% JD�K�����[�J@0�����@I��8k���&(I�O" ��xe���&(I�O" ��xe���&(O'�% ��("�R(e�qX"�a�ȎR  (�R�% ���+D V�d��	� |]���!��k" _�8gE@`E���	cө|E���5�3�h6ըm";����#�|�k��9p��S�Y�X �*OUٿ�@S�ԥgDk3_j��[�I}}�!��L���M������4ob���p"�O�e���Ѻ̪�H��H��e��~zь�������bh�˜���z4�~j���ց4�D���z��l��#�� �.�Ky������ 7 �:D�{���z+��ƺ�3E0�I0*l>��h����='Z��oW.�:���+�*D8��#�B��9"Ǒ�*��I�����X�Q�,�����}�����T���K�  � dg��s(�O��gր�L�! � ��s�Ci�Y烆��i���T�>�q�[a���e6"�1��'����\�M�dn�&�����+|Q�"�i1m�I�����%ͧӄ1���C�ZP�S%Tmث�B-���Ds�����Wq���FRw7ץo���������ۛ�S{�v߫b�.�fs}�eng?������%u}�Ow���G�Oz�:#��TI51�u�F�~�s�N�� \��ӻ& ���G}�dS$=W�0�Py� �U���$��+<W��͑Tӄ��[�2|H�|'��s ���tW��	�4BR�쓼<	j�JE>�T"8��z�x�rnv|C�� |&��?��	m��L�b��_<���^����/C-�3�D	>�4�^	�E��S�.{����{��B�9����Hs}��u��*��g&�����+�\�"쬪T��1��,�-N��3��������������#��l5&��}C��� � xxs��s��ۧ�����vo����" 5"�|f��\�V�ʧ�#�+<Е��b���}��N��\�G�l���5Uj�OǗ��Ԫ
��5��%��yYLo�G9���\��
�p���M��U'������4��.ua^��&�ܬ�o��ӹ"��|�@4����in�h�ۣ�����]��?�~���wf>�i�5z�Es����M�O�F��g3��r��o��� qp<��wA��~�����^ŕ^�[۱i��L�L�pCYE��Mu�g�c�i����/�1e�*��J���:�^wt��;( ��3{&Eg��xxz+�v' ��W�* @p�Da�U�f"�1/@5����Au���N�#�׹��������,�A���9]�t�>�r<G	�# �555�ؒ::�{���1��P�y��9��ӟ���Z>���r��Y��wDv��wAOV��G-��
7���(��\�OcG��Sx&�&p�� V>���Yn
����0*4����S�5MWW�#��l����nܶ��ޣ��́|� �>�;Y#ZK���@�g��� t�sv�L�7I���ꯠ�ĝƆ��X�q�=����H���%��q�g m^zR?�Wsȶ b�|� x!�TzN����\��~r޶ v�2s���O΋/# ���lҌ{������-��y	���� �� p}������ھ��������B�j�s�SJ���"�,]�Uj(|�O���4[9"-�۟��ۂ��D�ܧA�R��'xy���ȏF�x�I��(�]]�>��#C��r�/����$>H ��n������{i�R �T�%���q'p<�x�j)���8�� L �f��}�?-Ƌ��P_�zNK ��c=��{���x��.a) n�Y��}�v|�;�ߌ��vN�t��RN�T��'D���>}*������Z ����+be�	pq27�/m>i�g~Hc) �C�����A�/��L�*=�K�D�v'~�ʝ0l���E3�oӰ d;��ǝ��*7�����`f�mj�� Z9u��=C�u!p�I�5�b⃤q�.K(+�H?B��,Q�#@O�_���x�l	@���I���3S��5P��/k|I�kͷ%��S�[��6�bG	�&���#�a0� �^~n�l	 ؗ�z叀��%p��3>g#aђ� @��D+����Y+'���p�Ѷ��X��ꭼf,!��Z�(l8��_�� ���uԼ��:PF��'PZ���')��@���^�R�A���إ�I� }�S�I6�9F�cTP�R�!�}�<�;Ǐ�m��0>9u{�ǆ�W�ͣ��:�8�wUw�t���7����Yo` �
܏�<��qTs�΋�J�[	U��߹ѿ1	���$�0�3���Q�d1��M�i��k���^��x�p\��;��#����9J�.�0}ҍNiN��>5���Nt��Jt��5��n ��(�(D�E�P���/���7s\��\�
�Pf������� �	L ģ��QG��Cjۆ9�����OE���~�j��O���mm��֎�-d������@$���
P���*�
(����Vw���O�>R�z� �ὔ�t����"r'�m�Ğ1 :L�Ɯ���J �����E��MX)!��� yq?�D}�Q�6�]w@���8d�n��Q��r,�	��� �1�=��_F����: �zB�6Nb�������,\' ~j��Q%3� �>�ea�#s�[R�j�?{��uw �#} ���@���/
 ?�U	�{�D`���l�: ~Uچ��kA �)� ����Y�;  �0��j�C����%�&e�
�w�����������F �D ��Ҿ�Qk}E���m��������VK�A�; XLD�Id�G	���������d9J��U��� �d���[���,m�J���>u�����������V��kn�K�q��ԧ�����\'� ��4����mh��|~ь�51����?_�|&U^r܎��7�ʄАtΑ)Xƣ5���ПP~���'�X^Y�qW䫱�HjYs}zI4�Z�ˬ�:�țsz:^]n�R��#�V�d�3黠)��󧓣�\' U��*�)�GQ0�?������]����}�p) ,�헡�w�( � ���;lr��H�+�p�{E��a�/`��w���cԜW��T=��#y��,B�c�'>���bl�w�1���֖ͯ���.����h��]����n�����J< �|�{l�N�q%C�pD��T�����i��ڕP� ���[�|3T��8,��N��-�Hz�"p� ❸���s�!��%�ل�-��}���lk�Z(�M���\��$��#��%2�;ͧ��ҝ� ;�{;x#?�O	B���HgA_�\D�����-ń\f{u{S4#��#��\_.ZR�Ƥ�HS�$ꯓu�P�hFȉ=H OLg�pW`��|���Qȇ]T���d'���F��0a�I#>@��e�X6�\/ ����]D�[��$~� �sF�4��=!�X;��* T_����-&~�*�'��(;̟�2w��S�� �!n�<#�u���mB�w����B@ B�/�yF �O8��#���LB���J�! p_ o������KV��~o(���`��YO	@�j���+�N�Q���p����.,�y  %IDAT15������:��Oh�������	h��w��Փ��uh�I��<����ȒB��7C��ͪ'��H$'��h6��#�C �=����!ۇ�zV ��B-��]<�7 �� C��>�MmۦY֋"
�}cǲT�ӻ����y�{���A;t��\��� V���4�7�?�_ewi�@���eX�� ��g���eC_�g ��(�d	�k��ߵ�m�b�Peݶ{2.����mK��A@��:*����7Po�z��S��u��F o��M�� m����	R<~���l�J�m�����L��'�y |����V}B�?�L �:��Q�"p%y���!�7`���w���5�g����K(-ݡ�0i1/���x����3Z~���?��E�
  �����2�8�!�!���|���\��ѩ�dh��.�+4},�w�zmw�n-rs�N�"?�d��Ov����L��DW՘��{�¾��A��!ҕ �b(�4�S����`йZ:��y�ۑ;zI@A��o�-]��H�z��P�c�^bgO'�3�ų0�&?.�m���|�q�ݡ������n��CS��=^뻪���}���2\G�"���¹	���]45��D2�c=g�ߋp#���?#|���5XhB�Ԗd�b/�寻؟�� ��xw�7�5j�+#���B}�@v�Q�4�٪9ؚ߻7��l�0��_U�ޢFe��,�k��7d��[:�z��_8�JN ?�����5�l�C P��/���������tc�0���O�m�t��ZS_n��m�+��vg�$��@���o����x^��y~W�->���L:y �����������6>wxyo�C�
�=��;*^�/ 0�#��N2��q��0/��Zy'�~�ͩ����'N)�� �86��@?$_G%,�d �W;�{,G�����pSbKUW�qo��� ƈ���+��dՏ���&�y|��8z p5x �H��Y���:ÿ�QN�v�se|���ޜZ9��-0���D�	�<n�\����a7 �
�:^c�lW����۟�J��;��Nn�+�4��g!��Ȇ�Ż�m�Ϊ��ګwqz�"M"����ֵ�������,4�m@��\��u�k
��v���X�OA?ے��v_��z�c?
z�4T��֭��1����B��&�kXy4mnKW���{"/�%>2������q�~lK�jfKW�y-��u���Ė��Ɋ?�;q'_p�y3(ޢ�T'(
C���#������FW �z;5_���HI>>�<
���q4�5�B''����S��1��g�|����M?�*���v"�����[{/��#p�������Ȅ�e��U�Vķ�����OC(���m��x{��87���~a�,|�f�9�&>�9����-Y�7�6'n�p@>� �_�n�*ϻ ��sh2<=�#5���Sk���㏣c��	D ��J뷄I$�L	�>G`�
�_&¥\�o �u��&��;�#�f^W�G���!|���!�w��N�LC���,���)�ଖΪU�-��A�]�������Bz���Dg��DW�D2tc�?�b �j��W�y�]Zf��l�?����Ueƞe{�/}��+|#Ӷ�3���dh7�vƤ���>���C8�Y�Q��L��ɽ��٣�f�o���Yi����h�m�,�{sߙ�J.�  49�?D �)K�dD ��&��'���" ����WP"�����N@���
J@PP����	� �^Bb_A	P �[2�8�Q2�*�WKN�v������W	� �Zrb�#D �`���z���3E%����T%O�x����B����" ��7���" �����(��8%3�x���^G	� �)�y���k%&�:J�A8j�d&ƅ�`\0�I�J@�֒�ƅ�`\0�I�J@�֒�ƅ��	̒�g	� <[tb�D NP�<<K@�٢Ý  p����Y" ��;��  ��E�mP   IDAT \�8�ߣ23    IEND�B`�
```
```png
�PNG

   IHDR         �x��   IDATx��	|\u����9I�d&�TP�
^TD+"�I�+�W�*���K�*4������(
%�"\6�׋^k3��"
��
�LJ��������m�9�g^�4��9��y���w�L&�pB @ ��	 27�4�  ����@ 2(@ ���2 �@��� �
� � dL� ��	�]@ ��<�?�Q�E @ S�LM7�"� d]��	 �I�@ �	 24ٴ�  �u��O ���@ Ȍ  3SM� � Yش���G @ #��L4m"� d]`��	 �{p	@ �L 21�4�  �u�-�' l)�e@ 2 @ ��$�" �@���?��&\�  �@� ��bD Ⱥ�x� �S�:@ R.@ H�� �@��� 0��"�  �j@����@ �.���	 [��z@ R,@ H��� �@���?`�6܂  �@j ��ZC Ⱥ���' lK��@ H�  �K[ � Y�v��m�p+ � �  �rZi
@ ��� �=!nG @ ��N*-!� d]`�� �o� � �N� ��)�!@ ��L��D��@ ��	 R6���  �u���O ��[!�  �*@���f@ �.0��	 �b;@ R$@ H�d�
 �@�&�?`�Vl�  �@j ��JA Ⱥ�d�' LF�m@ H�  %I � Y�\���y�5 � �  �bi@ ��� 0Y1�G @ �L"- � d]`�� &o�=@ H�  �SH � Y�J����q@ .@ H�R> �@���?`jn�@ �D =}�  �u���O ���C @ ��O�#� d]`�� �n�=@ H�  �SG� � Y�N����q_@ *@ H��Q6 �@���?`z~�@ �D
 9m�  �u���O �� �G @ ��N%#� d]`�� �o�@ H�  qSF� � YhE��V(�@ &@ H؄Q. �@�Z�?�5��@ �D	 5]�  �u�V�O h�$�A @ A�M�"� d]�u� Zgɞ@ H�  1SE� � Yhe��Vj�/@ "@ H�DQ& �@�Z�?����@ �D 1M�  �u�V�O h�(�C @ �L%"� d]��� Zo�@ ��  �SD� � Y�@��@ �� b>A��  �u�`�' ��^@ ��  ��Cq � Y�@P��@ � b<9��  �u���' g˞@ ��  �SCa � Y�@���@ ��
 b:1��  �u�`�' ���@ ��  ��BQ � Y�@���@ �
 b8)��  �u���' o� � �N� �)� �X���QZ�~��C�K��������b��R�������ե��7{�K7]����k�(�g4W��}ݵEK������\*����{/����w�$W ��	 a(3	Wگ�{���b�̾b��ݵ����kw�o�q��{U���ιU�Vf�DV��Pq�HUY��*"�'���e�ն��sr��ɥ��E�g��;w�����{z�Ã}��r_�����PwvN �  fA X���rK�̷ԥ}�����u�=�nS�Vu�U�'�ur�����8,��hщ�:u�s���Z���Ra�{�B�%��+���b����� �3� �R��k؃}�=���=����z}����=�.w��c�u�h^�y�MT��3�ԕ3Ƽ�,��[��{�#�[�soR�J�lPuj��Wj{�1R#����i�����`�{࿧��7�`_�g�ǈ辢v�]R|R}���U��u�g+�H�G{���i-�a�M K�q����=��X;�T������Ɵ��B{��M�}�%�/�#Ri��T��m��B[.�q�\ ��� `bNl�@�}�u��-��oh�����T�3"��������v�w���cu��j'�<��pB q�L Ϛ�x�����}������mN:~�*���^d+����n��T��;�?���?�?|܉���v=l"@ ����!�|0ZR>Ԟ����8�>.��N-�#(m�ܫ���m��ّ���,��o-��!�b�0wG S��2-p�����u���`t��z�=�?�@:le	^`'�d��Ŏ��da��]�h�]͂@6 ٜw�Q`ɂ���g��6��7��t����%2}�����__*u�!#O��,�@ � �z3ZF���[~�=�|��g�i��?!*�g����y��~������Ky�`R��:[%@ h�$�A��
#/��˟x�~��~�`���9�lq]�L��,r�7��R:d��q����-vw��ŧ8��y�cqw�ɽ���?��~H��=м�^S>���(�~�z�K�B�v�J[��嚾b���B����ڝ���}ݵ�z��W������E��,��t����w���A�g�S,;3w������~��_cM��V�B�j�&�������7쟰�)�I	 &���o9��\�8ܳ�X{�=�/�+Կk*�����g8���ߪ�?�6�;V�%���3�Dy����Q�*����6����Nd?U�����-"�;'����Qy�]w�]^�yzy�s�ٳٚձ�j���O�������������3l��.K��e?T'?1���}aI�����opn�f��//-��pB p��  �o�ȈͿz�䐡�}��	��z�����Is�։��lR�V��N�kEd_���Q/;��~N�eV����
�ɏ���G��-Ծd�ۚox��S�4�=�[��������b�n+K:��շ�h������7�9Ǚ��k2�{�������������B�w:�[�y����k�ϰ�X�%�W�,<YU���2ϟ�3w���o,)�����z\{���x���B����Q����XD_"�2 �f����X��R�H�(SA�QI H�d{�_���C{��؃��uJ�n;<�=q�X;̞�7�aaf��ޙN�Uk�j����KJ�����9i����ߎr��c�~�������E�w��8�<o��ϥB�}'�W>]0�S�� -���k��b�����u#R�o�������`�J{��l�P	ە�h���5�9�7V��~�~����	��'�m�]����vW�&'�y�'��RX�;�ʧG�v�ui���a�Xi��/�4�{�#ۃ�Y���|m�j�;E�d? �5��9�|Ntv}̹�ߏ�GN<䡝��>��z�C��kWZ��k���QLB���W|��������Y�/�
�\���=&t����~���'z�;U�k�N'����+�a
����Ѷ�{,\�[~CT�W�TۣT�/Q�5���b
�p�L	��s����-_���i�eQ� 0A����[:�T���P�Vqr��4�N8�J��7�ZU�n�W�.j_��~�?���&���z������7{�b@Tf��+��7WŻ¾g����	�x	�1�O�q;������m2v���	+�1��9��f�ھ�v��g���Yxq�.)�^1s����C�����ʂ��T���V{�p��v���']��q����w-�g�����.��C�ٌ��x����������z��/l�н�������^�Z�O����>��\��^���b5� �������r�/,�Y��=��^���^f;��'�^���`Ge�����S)��nf_��B}�����v�1��vS�P�v�e��
D�x�@���z��]*�!?ֱ��?&�s�S�T�5���K��}���&Z��b}�?\�ŉM��"   IDAT;n��a;�!�Q��}zfO��R�XӰ�1�l 8�%���g��G���ٝ.�ό��P��^��x_q�l?d߾���
�S|q�*�Ͽ-&nF�����zG��M�^�-;s���~�š/��w�QE�����F�=ؒѝh���?\R>tQϝ��뒞{�v��E�Lۆ7�K�����cc��K����s�`���q|���R��"[�٦ޭ*�[D�CH�r��zu~t�_�u���Im�h��������f�ˌ�1��z)e0깈|��H} X2�~P�X��^����GF��!	�>�9])~�{� �Q��
<��Ƶ}��_0�;��Hm (|Qoa����3���ʂ �I`�&��
��O�S]��@�. ,�i~�k���_��k�'�@`k.�T��[����-���R �+>�c�P���ɝ��v[Y@ �D��gJ��ϻ�#S�ⰃT�����<i��^�=��#=��o� $F@Ϙ��O�풘�)4�� ��y���;J��%��e����D+R<�j��1]}\��}�H�@<z��Q��h&�����T��vO��E@��&ckJ�����}�S Q���B�3Ƽ�����IJU ���vu?��������A��RPb@i��s�p퇢�f��C|�R-�i/m~�T>*�]�\d� K��g��~���'@ 3�CT��W��'3-����4� P:d��v��.�� � �
�9u�+��9*{����b������Qq��Z#u!� �	�}��g������>c �
����QyR����R���T*�>e������B��z��F?C`A 6P9���v�f�q!!�*3V��l�J�[��D5 �@|��	�����J�(� �[>����DDjF �Pѥ}�zYĞ6�=8�MI nw�E (�V��C= �@���޾�H?! γ��" ���E�G�KDe �@|�!�T9/�R٣��7� ��=�F��pB ����R_�_�:`6�Y X����5.�&;]#� �p"'���n�^�[�⸟����z���N � -P���[���D"	 ����E�S)�m@ &.�"�-j���=�2x�x�z ���SU�-���IBU �@�TN��wA�� ܘo��]>��= �m�O�
C��6B<��k���b��N�qŠ.@ U�]P:d�R�ʹL � `��קliY��@`�m��}u����֍�%����*�w��!6��ʂ  ��,����-�ސ�������$�@ ��	���]T�~��⡝��[���s}����7<ש�&�Ԇ �Z��g��NO����TO�ě% ��X���X6 ������ƋO�&�@ ޻�A����Q�{s��Ȇ�s��K��y��.�%x �\�]�ҹ�"�@ ,����%=��y,��}�� ��#P �%�O�r_Y�еe�oz�\ � �w��s|�{6�K � Q����qW��Qב����Y�@f�:JE���@� �@���J���뜎�� ���7aE ������G��Z]R�JB�� ���@� �@fTvvc����fd� �� N.�.u�����6 �$��c��$�Z�Q]``��v����  �:վ�b�Olm�h������b�="� l"�.Y�]�&Wpv
I�K`@E�O
u"� �8�]f89�2 ��6���R%�K��{�Z
���X �FJ���"� �h4.ᣂ�ő�s� uOJ+}!� �Py��v~4�=�\�vX p�f%	�Z@ 6P�[2�v�&�p6e� q��H�7� �@vT���Ӌ�����t=�N�u�����EA� � �
ؑ�ykG����:ΧG � `G 6���N@ ��
�~�T�?%��O���mX P�z�0�@`K��#����Z.'_ � `��J> � 8���z	�H�m� ���V@ �4xm祡z��@`�9wￆ� �@�������7%�� kO� ��[Y@ R"�D?�x��)i'�m T��y] @ �T	�g�w�(U-���d�$�  ޝ�$�j@ ��	x��в�݌�����, x��}r�@`�O����'�m&6Kj������
d, �@����-���$}N �V꘨�&�@ԏ  ��'���K�pm&�HnӁ�&�:Wi~eE H���|?���JWW��&� ���<[�t� dD@u�9)#�n��$�h ��;��'��@ �&��.�q������4 ��F׫�Y�&�:@ �)
�c��)�7wKv��&M�˟'�#�	@ u�$�侃��2�P��SU�_�KZD �(���1��,6��� � yz�#_�@ }NNX�����X�;
% 8�7݌t� dZ��kGkGfK �݆ *s��
���s� ��xNe�x�s]|B	 ���|�p�++ �@�Tt����n��l���pmh`���/�ɗҀF � Oh��x�\W��@@�6�j_��ʂ  �>����4^G�.� ����� L] � l!�o_ᡗlqc*j h�R�-q�4ϳ"� �K�i�����ݤ���2Q�����V@ �t	�<�S�ZJg7��&�U]��pz�<+ �@�f�n��IUG�5����&�.�]g�K+��Y@ �#�Nߖ�n��Id���6蛌�.[Y@ R"�D�-9��g�����HӅ�@q�/�w�k;�Ώ�ʂ  �m=:%����H@S��:{о.'�}eA H��:}}
�آ�t]�< 49˕�W��c��Y@ R!��҂�sS�IJ��E h����ǩk�'`�y�@ ��o�!�l^}�.�& 4a��&�/��|F�!� � IP�7&����� ��.�����2;��, � �8pɂ���[�����|�@��<�Y�vo�8wu�2+ �@2�F�?�Yy���e h�����[���2'���eV@ �
�wx�~B�i�"��1�J�U}�]~�V@ �$	8w��-I%g�����D��~�>��;�}[Y@ �"����1������ǩ3�W%" 4�Ͻf��ʕ�kD��o'@ �������2 ��re��r%w�6f�PEW?v=_@ b*����&TVZ7J\ xl"����U%���:Ʈ�
 G':?�ue�����&�^�ʬ�ڳ|uU�'�]�W@ �x����z�{z<��l��>��95g߸G}�`�7�+���7ځ�~։��y+ �@��6�7F?�U�� �iG~c�{*�=�o���g��wE�����:��[���}]���r������� Ұ�Y@ ��?��%ik��M] �t��WϺ��:�%��+�����٥���>�6���|'����qι�8q8'�m��s��O�z�k~ �zE�=��@57���o��n�z���`��r�k_o�]+�{��;�W�8WVэ���y@ +*R�J�I�3�`�IX&�7�p`��5�9�)Ws��v�a��T�o�t��v��.�l���%͏(^�Rk��w���_��r�k��9�S�v�Y�h�i�[n�e@ ��$j��מ�>�]m�@��ia�z;��;*`/EDY	c#� �̾����Ge��
 �J��!�{���.�@ �	8�D��O �n����&N�`x�D "P��E20��+@ �%�+��p�~$F@ b"���1�de�@�s���"��@ �P��}C���)@ �&O�7�_��7Y�(�����>1�d�eda@Գ�rg�%0> ������]C�a�#@ �P�7�!�{���#� �h��Z�VI6�' D?��&@�s@ ��/^�z{� �'��I   IDAT���d���` @ _c �8�A �x�s#��� �@x�Oo0Fږ `[:!ܦ*�0C � �Pq{Ǣ���� ϵ:�B`�S�� ��# ����� ��	�V'2� � �D@��Kb|�Ri��g۩�q	� �(�:�����V`B�z4ı
�\`憶���&� �r?q0U� �D�A �4�d����� O��{ "��G �p<�?5�'6Zֶ" D=�NQ��� �@��<%��l\��,�]��A ���@ Nv�C�א�K�����<@�s�� ���!��p� �A	�*�%�0�b ���_�K�% B'�|@����@ �*nǴ���� ϒ:���0<�+�Dvw�퍖��	 ϻ/�����@ \;�6� `<��s�q8�B �p�7?GM�h�7��O�D�� �~g� ��e�;$ D<G�mΏ��G 2,���	 �}��PX�s�� ��:�cO����$��ޕ�� 7#!�@Tb��#>1	O����c@��#�@�<��N����'��z��'���` /&�=q���&!:�GFmw���� �@��2�kl[% D<5���� "��G ��\,@�]�m<@�3� "��G ����I�!��7`<�������c(@ z��oH�O���n&G "��G ��.�e���7� ��G�fv�������'Q����a���	Ƅ �0>�/�)��>���7�# �q���u�ǀ�ggD�X`�}� ��c2< �h��F �x���@�웍H ،#�O��' |vFD ���Ջ, D�zl�' D<�Vj�M�.�2U�Ѯ���# <�$�k�! �q�D`��qF$�>&@ xL"ү�!��Y`��_�|�� [�Dq�9@ �	�I4����L �Ť��bQE � !	8��zk� �&���s8�B ��$ D�u��' �`>�skcP% � �	p 4�D �*Mx7�* <nFB �X��@,ڎQ�L�@��@ D�k�k�!z�7`<���S����d<@ J�6�_���x�M ���h����A	 �@h�S� ��=�@��]B��ױ{B��@ ��|�5 D�n,�' �aZ|wwʠ@ ,�# aQou�Vi»a��=��	�^@UB<}�q���cQY��5{����4@C`A �l4|73�ƷK@l����R(X�� � n%��' �f�[bS
� � �꬀�`��  l(���m��H �@��w!�h������̎ʭq)�:@ ������`��  l�'�[���~�`� D.���Qcl]� �u�Po9w��?���Pe0@ "'nADC3�? �������q��@ ��Td����8�}[�m�|���  �l�p ��F}et�32 F�*�@8!�@f�Iϒ����'fr�� ��ݜ�C�e� �@<t��wzZ<kKU�ͱ��|@��R@ ]\*�����k��v4��=���B��������V@ K�lt�m���I'���!K�G�+ J�-ƾ�rm�ʍ[\�E@ sT�3cc��
�����%��8����Y}���_;�T>��X_�[�}�T�����W����][�d�Т���k{�#/�?y2c�a[@�f�w�W1+�r@ �0�E��m���u��T�=�[��Yꮯ��+������b�%{頷X�-�n��������y�#Q�����B�'EܲMW�r�sr��y��s�U���?J��_K�ڗ{���[z�C�
��(�" D���1��y37"� ��iGF����W�����-����^��vy?;����\����i�<Z���;�n{$x�߲������R� b6���kcV� � �x$x�{��/̘��_����z��W/�ߥ�W	 1��޹��ɒ�P�ʢ@ 1�|=Î8\q�N��J��g��� ��i���V�U� � <&�Dv�z{_���E=w�z�$}% �p�<�~ò(	H�@�%�����访[ڽ�ea�܊� �Pl�>|i Zl��@ �����]�'�b��%=.�8��/�բ-��@��fYۂ]�@ S�6����o\Z�ϋ����N ��S�[9�U�ʀ � ���TK��;�����O Zx��w�� S��~ �Q����f���z������T�e�-EbrY�( &sA ��T�ܥ{�5�����3��}@��S���nw"|*���dO �;q�p3�q�� @�c��ɗ;�W@ ��
<����,�+N �4[��:r�U�ʂ  �M��ߨ��|�ria���R) .31N+Vj�9��qn�*@ ��	�}|�v�!#O�C��8��6j�V�q37!� �Hr�>������]��� �lg|�����l�� � IPy������]��@ ��7My��;�ɭ1/��@ �8�ʁ����e�"{�l�NWt%{��gd@ ��_[�}0�}ow���E��?�}T� �S �U����0|h= �P��+������J[Y@ �%��~a�<�v[��ŧ8���iS�+wC R,�����];)�N a�Oq���nњpB H����R*ԟfc�0��1֧�:��.��.�+ �:�5�S���� ��4����i#� �T��[�" ��a��ί̺Cԭ�6�C �'���g6:$�� ����%�d�E ���9���Ķ��V����~o��Kl�1[Y@ �L��y�j���Fo�0�[8ƊU�u*_o�.� �@�|��e!|D0 F�>�R�E>5�m�H�@z�Ru{�WyE� �`���7��U�]"� �@����A�A Z8��;uh����@���t���������U���ʂ  �2٥���=�l� �n��V���Q���@ �	
dc3��xW�� ��x����kD��	@ �zd�M ��a��3B�!@ ��d�'�%=.T���dC�����"��!�0 � !	��z�#ϖ�N��`�ܭ�ɇl<;`�� � ��XsN�T���dC��U]��+B��@ BPm<9�a AɆ�����2q�P��2 �@�s"��3 (ِ�{�'=�T�yX�C P�~�?)�� ���`�����۰��H�@�Z��� �7�����sq��=� �@<�5�x�K�*W�.��cX%!� ���}'���O��1�W�l����# �@,TdVP����d#�oy͜[��OEXC#� -���T{�l��)R�+�1+�6[Y@ *�Ď�T;  بw��np�������-���7ջԎ�o��;ר��_��*@ ���֦p�	݅ 0!��n4���t��n[Y@ ��
d�l]P� ����~ϾJ����Ĥ�@ ������|�[ &n��-�ծ���E�m��@ �Yn^���]	 ����> N��H��� �B��{P� ����~�cAzF�ʢ@`;پycc�A	 ����~w�t���%�@  �#Vw�KP� ����~������S��+meA b/������? Hݘ�۹�_ԇcZe!� <"��K@� ���n�]7���s�Ԇ �dݠ�GA
 �ԍ�˕�V�Ŷ� � �L����g�r- f�f9�\��}�c2 0Q�,o��߹le�/� 2�v�]߮�#�MؾY@ &+��O�.�ݞ 0Y��m�|0���[Y@ �	d�wsyU��A �N��˕�run �R" �z�_�vT   IDAT��c@꿕&�`��d�9}xb[� �@���n�C�/��? 儌�_�}L�\��r)H��=����7�Q�1@�	�9'N �@dY��F��φ�= ,鄌�be�nߓ׊hM8!� �&`O�λp圵aH K:A�?���9�zݘ��)R"��6�q���w@��	k�����[���)H���~x���B=�J H�K���W�_�CR�?# � �	d����'��	 a�'l<;`��\��$�r�E  ��~�;�E��(�6�.�|�8�z�ʦ\H�@�J�E��_=��(�& D���1�����-N䊄�N� �@���)��EU  *���{�/�����i�J�\H�@�
տ;����/�FT] ��Oท�������kE�5	,��@ ���y^ۡ��VEY J��}�UZoH�UV�oleA Z&��9��y�f�!�~	 Q�@�?��ú��\��!��S2 ��srn���hdl20`�N\�ܕ��l��{��V@`�Y��^<P͟�N	 q���1pݜ�G��十	�=JF �0<��J�]a������m��lK����;?�{���ﶶ�6@`[��͉/�-=���[����H�Yv�n���΅V��meA xT`}���,v�?z1^� �5���6�Ͻ^��Vb��p�H }�:qw8���k�|7�� �:3	���t���䏲o�X>%#� �p"z�z�?p`0�7I �S+��j5P�:VD?$�@ �	�e{������f�K�:�� q����g�>��-Ih��� LF`�8=}��ܼ�?�o2E &�Ŷ��:_E��;��ʂ �#�૜�N�72���}��Ա$uC H�l%���J����N\��%���@ ";��3Uy������wDTƴ�% L��;OD`E%�Cm���'�=� �@v�韝�k*��'8���(�~�%���9�6�苭���ʂ $O��9�u�6Pɥ�3O ��Ll�v�l�.�S�o$�	
G �
$aWn���z���[��O:g�^�P�Dj$ LD�mZ&��� ;lv�:����!� �U��������B�|o�[$ �Z��MH���u�����[Y@ ��l��F��e'�s���w�X��;�u��(@���V����^O{�������F��^��{��!�U���6�h�������*snK{����p��k��6PɿՉ�WD�;��)�v�������k�+�+ʕ\�}������eqّ>,N�}�{�@%wl��οť���  -��'$`!೾�׈��&t6B���T��v��[�\�_���=�99�n{�V���k-|�~�<�\�-]���v9S S��fWT�?���?�*�@�N��hx�����ֆ=g�����ܸ~h/_�B;j��m����jU�����{��^�N����! �wK|��w���XUmeA 0{��= ,)W󇟳������2�b��v��0;l�4Q�QY;���Md�m䋝���܋����oyN�� 컂%^�D��`�S�C���,+�N3^rT3I�u�s����'y��7o6.v��h��U���+^�z\g[g¹�~~�m�ʱۘk>���`��'��{���,���cϲ�=r4���jn�\�=(�~c���=����0���Z��>���\��蟅S�?����}מ�ծ3����,&�c �:3���@َx�v���>��+9���F-L�=�~����!��+��^N�d���[���J���օ�fA\��7��S=��tw//W�����gmb��� ���Ly�Ug�Ŏ�b���*�#�	������g��\��o��?�N}�r5w�=�i����<�bNW��O}��s����Okc������j��͟�l�U� �A��
��/��3����[%��E���pν��o���	�ɯյ�}�u_����H�\q�Q#�E���2��^oszF���\X��8'����W��kw@ ��'�����=0�_��k�^��~�? �5���0{�?b��us #Li��g�vd�t{�:P��Y8݉�0��e�NCN�
y�xm���A6�9�����`:% ��^CX�:��ap���v����d��4���w�s������/���"���G*����쵁�E�bI���m/N��濢N�u���yrn�f���?S^5��mߙ['#@ ����R�u�2���뚿�%��")*h��Ĺ�vn�=c���(��Z������VT�_-Wr�P���FcgQy�9�B�O��í36�s�!�y�+�ɢ]����.h����k#6���/e��NF������Teqr���
��k����r��s�V���I\��I��?�g�g�3��p�2�O�	����U�D~��i>��d=\���kSy�����l���6�o�}��%@��@�T��?�I�*�_�>�����r�]U��r�������f�柤��s˕��l}^%7��~�l{8=�9wT�臈�p�V��8�W�]#"_�������������l}G���|0����  "x�V`�2�6{���惄8�*���{HC�z�s�7�u���3�o �l�܇�k�7T��Q����A�@���纚�)�`�\{�=T�w�s�nq�~]���D��,4�#AY�-"?�u�6����߳ۿj_/r���3m��ڑ��K�sr���C�r%�{��Y��?�\��P������M�f�?KL���A"�|�(W�;���6@��}aI�@�]�'4dl��`ճ��&�	L^�H���˕���L����E����-$��c�@%�y�\�=0�ծ;�\ɿ��C���_����~�}}�@��x{��4��'�󗕛�{`�$��Y� �lG� � nN����٫��+��:���p::Ko*z�=�}���sm]~~e�u��6��c� q��	T�U�W�ܱ���}��G�	����vn���:'�z^���:��J���[#��D �b�T	|f圵v���`A@N��H�V�h~c��v} �ԁj���V��C4e0jP�7~���	�(���C��s�����n�>�r�=�=�2<��#0��_(W��<|~�-����O"�'X� ��ɣ��
\X��/�75T�{:�����6�,�p2b����;wd�}���j�]+V�Yݪݳ��
PW q�j�\``0�S[�\k��n�#�b���Vc|'��������:P�:jE��ۗ���p|��2�/@ H���4.[���鯔+]����vT�#���˻leW@�����\�xr�=�ߣ<�[d��_x�#��{/�L� ��S� �y��
��R�䮰g�ǖ+��<�+��}DD������-�|_^�q}����jW��*���׌��>�  �s^�*��_)�v�Q��^Ґ�g����k�!��&����7qr�S�����*�WT�V���ڞ��w������L\ q��J�����(W�\<P�z�=�}�����z��㭑��s�ϫ���'lњ�^c�⻥����K!��^vh�e����;\/�@ q��M'A`�9��v�ܞ�g�G��]ͿX�������R'��>~"����[�\��ia�*r��ܱ��ai����[�S^���_�=�|)ĶgA`Bl_@|��R(���r5wq5�����3��,��nv8}���Z���|�ֳ��2c���~GD�'W�hUD����R�����!"���K׼�'N��v�r��v�<;�Q;�=����Q�hG(�Ӑ�N6���γ�����?eE�낁j���Wf5�kwcA �4
 �8���8��A�����0��reΥ����l}�=����dA����U�j�ev�h����+W�����L=��r%?�\�j���J�5v��9�o��Ǘ�]��+��� ���9W��T��-�W���O�wK�
��8 �<;Ԇ  �@@��`�- �u���  ��qR��  �IDATCu � "@ ���"� Y��� �>Cԇ  �@ � P�% �u���  �sD� � �\� �rRv� d]��� @ H�,Q# � -  ���!� Y��d �1OT�  �@K -�dg �@��?)���u"�  �B@1� �u�O�  9sE� � �L� �2Jv� d]���$@ H�lQ+ � -  ��� � Y��d	 �5_T�  �@K -ad' �@��?i����"�  �@� �u�O�  ysF� � L[� 0mBv� d]���(@ H�Q3 � �  L��#� Y��d
 �9oT�  ��� ���� �@��?����u#�  0�4�+ �u�O�  �sG� � LY� 0e:� d]���,@ H��Q; � S  L��!� Y��d �=T�  ��� Sb�N �@��?���� �#�  0�и �u�O�  �sH � LZ� 0i2� d]��� @ H�,� � �  L��@ ���: �G�@ @`R�Iq�1 �u�O�  -3I � LB� 0	,6E �.@��  �g.�@ �	 &Lņ �@��?M�4�&� �  0A���Ⱥ ��K� ����@ &$@ �!� Y���	 �6��  �� @b@ ���>@�攎@ خ `�Dl� d]���(@ H�� � �  l��@ ���: �W�B @`��m�p# �u�O�  �3K_ � lC� �nB �.@��  �wn�@ ��
 �J� �@��?��4�.�!�  ��V`�Ⱥ ��[� ����;@ �  �� �@��?����0�!�  0� `�B �.@��  ���@ �' �@� �@��?�,�2="�  �� `."� Y��l �1�t�  ��f��8�� d]���"@ ��L�' � � 6��, �u�ώ  ;sM� � <.@ x��3 �@��?K�,�6�"�  �O�?!�� d]���%@ ��|�- � � a�@ ���5@�f�~@ 0�!� � Y���	 �7�t�  �� �&@ 2/ @ Y�uzF ȼ  ��  �@��?��l�;]#� d\� ��o �G ���V Y�y�F ȴ  ��O� �u�Ϯ  �sO� �   dx�i�.@�Y  dy��@ ���N=�#�@��?��  ��p���   IDAT ��a�*�    IEND�B`�
```
```png
�PNG

   IHDR   �   �   =�2   IDATx�]	x[Օ>�>Ɏ-�I �P�vXJ)m
����0L��2�LX���R�Hi'.(�8^�Ne�����b)� m�����ŖdǒޙsI�Ȳ-%��w�w��{W��{�~�{�}O�I���B�șb
�ZX�*�Юr�#��
!��ܙ�o�'����=c��3����Bho��3V
�=�jo*����=c� <�m*�����d��K����Bh8�K&
���m�*�����db��b�B���b×���Bv܀��^�D@�%����n�b>lpIBh�8R�؍�z7����.q���!�n��%�]�H1c7B��8d{�����A�U�F@�7FR�A��,Q�o��}c$5���A�U�F`p��}��4ԒorզuU�3#�;��*'���+\�~Jx̎�Չ����!|�� B�l�M���qY8�X��mM'�>U�A)x�,k�W�^o�R� �{�>	�����;��FB�+&�j?$匀:g����qOY�k;������ԇ:~��ȧp���F>�4�żo�V�oF����� '#��UQ���m�1�����v�5�M��+��R)̊ a]ughĆ~�Ng^��Ǚ�s���c9#�|�� �����R�s���᪎ȵ����KB�� 9�a�iT��qO.�����JD���k눙Os����K+�������áĜH(������{��M8���XZk�AWD�;��ꎟ���<�/
���	%��d�_�k��s�������9��z�ʑ�}��N��:y�a�E�|�G�Ǹ��r>�X�S���<���?�L�](����!!t_�GFSy�6yL]u�*�z���6f>�	�W������L� ��W�d�	@0���H�8�Tԇ�1R�x�ۺ3R�^3���=�$ �s�-�N[WTW�������������� ���H��
[�k���qj�65f�y��O��Hh�����1�Wo�$�?���7£�x �?�������ӹ�٨^�J�7�ʃ����H��Yg��@.ijU��G?�Q��$�*�sL��,0�T�Ku��Mz�2A����*tg:BN%Gׇ�O!��D�%@T�C�9U+D�܇v���������*O����m�xI��{�ky�#�]�µL�I�px�/$���=���C�<��p�+�93��P��HM���+�_� CYw=d3�x����K/��wt)|3\�K�x��"Pq�&q	���|+�r���ơ�����@�^_�~������)��Z�:��^`��r4�[}u�#d$���}��X�I�m�'�r�2��L���5B $��t������&��z3$/�7�,=?�C��3o b����C���2A]Md�e]���$J�8�����}�b�&��a��вm_ʠ	=�<z��T���C:�̋AҠ@�@��>�� 4�l`��ր�W����+�����T�"�#p6�!>g�,2_��i8 B�y�ȍ�{�I���@�u��ĝu�䪩V�	��l��0�}8���3(�*�X8q�UgZ����"��P׉��<�k�UHr8{�!��w�:�O��L�ɕɣ-H=�Րc�j�@�o��*.r��#GBZ��x �Hr#~@�B�w�LN60'B�U����n }��7"�C�, �D��<���C��Eo�"^�t�s��'u� @C�ϳ�C��!'n�9Ӕm�J�W%+IṦ(,zD��54sL�{�}�g10�]K�#p$�z��-#s�јjY	�y9�e����AD�N��K�"G�k�Jh��Ht�ADQ�2"8ע�$�T�S���VV:��9�g+R�� �m�U��	j*�'Ҡ||��VG�s;De?9�r�#���J�"�������� :��k�x.d%tʦ v���#��3*�ab�V�W=���S��<� �ZD�������d���t#D��#�s��B7M�n?������N��8 |<���L7!@@EZ3M��KVB/�Ut�6�q�SĖA#�����t+j +�u�<��Fo%����R���7�OBWĂo���.M:�p�?�c�	}���`�:&�fӔ}ÿ�J�p�z��q����.�� �~���  �t��Q��rmN�@�S��P.ARD'�A��i=����Hh����?�15�o�,�u�)[�hq΄N�����*ث���x�>/7�^�~Z��l(��!����L�7 P�4�sb�&�v�}m����X�,�iH<�9��m��~�e@���[���R-�ҵͱ�߱aWr�����	H?�D|���"��$ ��x#�d�����	��M���;���,��Z�:/�)D4�~j̶ǃR�m_ �	싀�f"�$9B��I���ڠeo�L4Ɔ�y���UM���x���%Vv{s���5��.m^Q�Ue+��%m-��g9��K����$�"�'N?���F�Ժ��m�a�?Xڊs�s��XR�����!'�v�e�u����P���(�`�1��l���(Àtrq��TF����Ahk�!�s"FP��J ��
t��N)�P] '�Y^�:B��N��ɥ}#�S:@����R�<x�1��Ow��š��B_[�O�C���+�X����s�_�zF���AK���s�ۦ��߲
�IN�Ah'_Y蕎���J�!`�z@1B�]����h$/�C���,f�m����Dh38�-�V�4��FZ#��/��d�!��ʡ|�j��L#�3� ��G�̦86�����{@�'�/
A��mm�ʀz�ߺ�E!�<ϱ�H�B��l��+�Yݙ� '}���,����lx���}���IH���[�;!���<�ޠ��w�+�R%��ֈU���C�w���0 �U$s诐C@�N�O�D֢�	���hDp4B	�@e���	�e��[u��I���=`l�)�g PQl�b���Dh+S�"�f�&��l�?Ӏ�5���V�6��z��?��i��Ke^	����B�֏�3uL�*ꨍ�և:&օ�3���Y�5�)���K��+'������(o_0�л�C�wo�աT����3h��m�?�rz`��ˋ{�@�VD�ID\����+*I,���/�`���dy<�x*��IS��-@	 ��e��r��/�C�4 ���c���~;R�^�PK>>�E�tן� �9 W��$���o?K��9�o�Q�ֿh�e�ۋ���=g ��m�cSB;���0�ݣ<�s�Mq�(Ԑ�X�M����#4Q�M6fg����D@�5�8?�*w����ZeR�ؐ�r5@�lH�X�_\�a߶G覵�����������A��U]��b�q��Jo�|�dڡ���� �����}Fi#	��z�[-ՖHz �$\��N�~�#	���K�RW֤� ���wJQ'۾�/��%twW�z"z�/�}� @�g��>�Yl,���I��y�<q�'�'�;�Y� Kh����JWs�^��BjA�u�Tq6�&�V��������ޗ�u�į0�7c�'t늒O�Ƈؕ6gO#�>�AV�f}�쐊��%<z�?�H]7"@v:Y�� 4�}m�[)�E|�_E<� &l����R� ��^����`.���,�I��?�O�9���6QL�K�$Ie�l�;�������=�$7e3J�s+�+�m�f���y���O��� �@�w �y����O����o��=�w�ޗ��h�����ZGZU�����&�\$j@\��ϟXi�)K�6L��� | ���FA�n��Ƶ�-���XBk㚖��~�����v!��e����.x 9PC��ф��-��n�K���eBjF�EB��ЯƷ��yU�����߆;����!��Dj�G��B��˙������_�w�
B�����v�15���c�Pd�)��ɶ.]���C�gr���z���m�����X���C�"�  �IDATh\�'���ZV��,\Eh��_��Ѕ�7�F����3�"#�s-"�[�,pBk��Ŷ���� R6s\Ghmlӻ�ص V�s�x#�|A�uE.[�`����[F�׫V��ޕ�֠ /�,Xx�V�l Z�e���<��<��9�EDϰ�&�L}�=�>��h�絮(���1�zZ\K�=�h��4�C���*�y��[z�G�˻�s�c�W���ed�#�<��L1�L7 �7�/������h�͍�ܜo�\OhX����h�O8z����T�s�����������_Þ��]�ղ�|uS������(�x+�O�n7�˻�z�D�d' >mc��ye���hpټ���� �x���E�y���B�o<�ѫ �~�]�6� h!�8U<\�n �ԑ^�d�Mے��%Dp/ �ĹPr�uق�QjA��
�3�������:�[p�w�)�ayÂh�
|�2�apH�G�LftwgpzS4�f�J��g��W��&��B+��m����9�q�|̣�C�0}jT	���������X�� �nrM�#�`tK�l�"U<$ ;v�~�V�+|�Y�v�Zs4p}����^���@{I��CO��*�G�	���H_��0�ی�e��t&���'���i=�����l�Z�]ӕ�����<���S�G����=1�hhC�;W?��IB�qS�tMwg鵨��������@�_�����^�ٺ���������7��h\|��:�b�729oo�k��hp&���xrs�lJK48�ߟ�ۻ�W]+�>��' Ȱ��	m�ZW	�xɦ��eGݚ�E@� p�U��W��2�; =�����2ugLs���Ek�8��$����L�TK�l1�����|�5�!�����Rng�l�ж#`�?��9*^�-]���l���� x�Vͼ�۴*�˦h`"��!]A6^��kd"��. ��:o�Ϻ\g�u�*>��?(�[� �=�G�D��������W6��A! ��|s�ö��
<���D<}���~8l����,PUHv�ʨ��h��lu��8�"�T3y�y*Ѹ7��k��#*�zR��^���ֶ������u�F�*4��W.X]��96�ՖX��l5�w4 O^ziC��:�x:�57*+�v�W=l����w��Bh7z��6	�=�|7�.�v�W=l��W�K�SB;�s�w��{�E
���ک��{E@�+,R�T��N����+�&t��H� `BhC!j�!t~p�VA@m�#D��  ���Ҋ!�q��j8R%!�#�&J!􁐑rG" �v��D�! �>2R�H�Ўt�(} ��B&[��g,Bhc]#�!�@P�s�E@m�kD��  �jr����u�(6�M�� �yC@�7(�!B���!o���4d�  ��/���   IDAT �Z�×9�J    IEND�B`�
```
```png
�PNG

   IHDR         ��a  \IDATx̑�J�P�ϹU���"�C�;:��)蠃�K�i7��A�(����:(��T�I\���M��9���hK�.^�㞜�O�����D>�]av�l��˸	yz�ɴ��\������0O�WݻE���G!���g�B!qj�}����^����,��U�����D�
�jD��j��a�3o���O�OUPϩ�1һ*��4�4 ���g�н'v��$-`��������9��	����U��0�?z���\��h	���f��&�T$K�/�D�'�:m�S���ˀ�S��vd��e�z�jٓOf]�ZN��r"�e@_��5��_��  ���b�   IDAT ��u!�{�    IEND�B`�
```
```png
�PNG

   IHDR           szz�  �IDATx��Kaǿϴ���V���~@t��ڝ�k�Ad��rg��/�� p�.�4%���̚ ]�C!u��E��J����� �9�n�D���̼?��}?<��;�r����������uf,�e���O$��L"��˵�6|���`��@k�&c�V1}.�3��+$��,dK�3a�����[�nt�Dt���hx�vM���&TX���l�^�O��~1=+���#D�(@q��&h� �I�$V����J���
J=�k��1z���K@�dK�T��p�/�$��tuHf˹z pi��h=sѽ����� Ki,HlD���|����r�o�T�0�3 ^KVY��~f���}Ҳ�������q�~ �BLG���~���ة#-���U�2.߁<}�_ I��@��LHN��,y��m��D��n�i�O&������Ǌv� �ҳ�$�'#�!���z�<�=��}f��Sf|R���a�}ە�/��x�1_W��	m���P�V&��pG��%���(ϲ J�r�n0zT;��vY�x���J��	 �5��$Қ�� uo�3���	�q��P��H�p]��B_�9�a�j ��c��V���h�@4Є�A��X���iO���p��Dc)'�Ӳ#�$�H���D���}+��@Jz����cˉ\�����;NJ4���&��"��VP�{l��Xk�  ����g=   IDAT �,�AI�!5    IEND�B`�
```
```png
�PNG

   IHDR     X   ���   sRGB ���   gAMA  ���a   	pHYs  �  ��o�d   IDATx^��yp������ϳǓ���&�+B����(��Tk+j������(ұ�
���)�c�J��3hՎ�t�t �"PL9!6�&�#{?��?��Lvɱ�*��k�qx��}�}��~���)8�$������F�ɾ@DDDDD4DDDDD�DDDDD�DDDDD�DDDDD�a�ݖ���/���/j��������(�Ӗ�����('�����('�����('�����('�����('�����('�����('�����('�����('�����('�����('�����('�����('�����('�����('�����('���Hf_<Q�$A�e�t:h�Zh4���������NR2�D"�@,C4E8F(B<��zJ����d2�l6#///;������ΰ`0�ߏ@ ��tRN*x0�L�����t�`0�P(�H$�X,EQ�L��#�����h� @Eh�Z��zȲ�ѡ�F���{ʂ�
$I����d �b1x�^��~$���DDDDDt�h4��fX�Vh�Z @ ���9��L�dYFaa!DQD"�@OO�^ov6"""""��Z�p8�h4Pn��P(;[�F<�L&��I]]]'���#I�����Ln����1�U��hT�׋�G�2p """":���q=zT�-TXX�ј�-'9z�^�����ݝ��������b������R�^���2������|� ���Ǔ�LDDDDD� ����A�����<����	�^�h4�����d""""":�tuu!�B����tf'k��A���f��a""""":����6�-�̶�<X�V ���;�-��������
�����m�\<�����vW"""""��#�ƷX,E1;yPC�ӣ�� ��hv2�1����0�IDD4E�Q�A`@�$C�C$N� 	""""":������H�dYR#DDDDD4������H�z=A@,��(��D���j���R��:��+�Á���S~_"":9:�v�=c�,�p89���&�2�N�v�!:�(��X,Ar:4n��A���yPDgڸq�PYY���
TTT`�ĉ����9"�,˰��9��љSXX���ǣ��L��r�PQQ�ݞ�7W.�����&0Dt�t�?g��A�$ @<�N":#�~?v�܉�;w���z������T鞥\F�9�={�d��n��G�3e2��t:Gj�V��6�L�������9hE����t:G��ѹD�\�a�J��':�m�r7}}�gW��N":����`8���Hf_t8������EOOOv2�iU[[�X,���: �����B__��EJ&�EEE����j�������CII	�=������K.�D�U��� mmm���7n�:
�H$p��1����=&L� �V��{����
��X:��MMM�;��3R�� Pmmm���FUU�N'b�$I���CCCl6***��?�C�Ѡ���$����d��H���<��N�����ttt g'�veee(,,D"�@$A}}=JJJ�2���������H$���H$(++Cww7:�q����r!
�d2A�� �H b߾}�+**B"��F��F�A @SS***�����5�L�СCj���� ���uuu�=�I�4p:����/ʲ�T�EY�!�2��0&tƥG��$����t:�����Q����#G�����������F}}=��8�� t:$I���AAA�v;zzz �F#�^/dYFcc#���`6�a�X��z�t:�H$����!�"��0�����x�g
��q��`P��������v���pv�]]]8x� DQ��fC8����`@cc#Z[[�S�� 
��ߏ}��!��rAE5�":Y�$�l6���s��'�V+�F#<,$IB2�T�삂��ա��V�&�	����e.�Z�.����hll�F��,�hmmEkk�q�3����������(
�N'��$t:4<������C�$$	����|�E��fC4E<�NBjD��l5����Ӗ��׌F����l6����R�����@j[1�F�^���*X,��^�yyy��l�X,���S������x0�(�0��
��(((�ĉ���p�ر�l@�'*�"
!�B��"����,������E�(f|EQ����JL�$����Z`;#�K����N��Ӆ��$JJJP^^A���`�ZՑ���b�A���g�s(�����B!$	h�Z=z^�UUU�����`�$I괎t�Qww�:_|�:�g��X����J�<�ٳmmm�Y��4#��Ȧ�����׫��F�>��L�PuuuhjjB4EAA����\9���c�����tp�����T��e�D"�N�J�z�#-���9���˾d2s�u8Fww7���ɒL&!I�q��F뢋.Bqq1�~?��ڎ��^��$��������0�EASS|>DQ���E0��� �����8b�l6DQ��l��(��(�j��e&�I]a�Z1a�u8���Z�v��
�ɔ��H$���uvv���@�euNm:��p@���b�@��qu�lkk+<��FD4�B!��n�t:�1�D �"<Z[[�^~ (//�$I8t�t:JJJ�T9�a����"5�-"��:R|��!���#��Y,�����B�;W'�\�@g��b��Z��l6dYFoo/"���0DQ���Bii)�v;��(�n7��8$I��fC ��#G�T�o2���ׇ���l6����(���^���EEEp�\@��ȑ#����g�s�;::�^�t ��hP\\���|$	�������f���� ���j���ׇ��^��f����e ���Dii������Hd�O���q�����<���!
! //z�^]�i�XP\\����t:���fCGG�;I��r���h����I��3�L��t(++��jE @[[�Z-���q��;��b1������\���<$	�����$���h���m���ٌ`0xB;ZȲI�]�6�}EQ��dB 8.m�Q��������;//�d2���/�(�hiiRg^8477���f��H���)g�nKt��e� �'㞨��2u'�`0x\�,�6�X~�L&������===0������3v��� :����iK4f���.�C��q�����(
�^�iE��A�����\.&L����j8�N�����~�����B��I�+������q��z�7��ը���(��봡��s�������������I�jooG[[A��`@OOOFO�:n�����5��������Y��L&��ֆ����Dc�-�iKDD�Ѵ�9�@DDDDD9���h��ٰX,��|��ϲ�ϸ��j̙3������x�����ؘ��3��9s&�n7�oߞ���Ƒ	���i4m籩S���g�Eaaav�����UW]�����������1u�T���(**-[�h�" �ҥK��  ���?⥗^ʺ��[�p!~��_�n��SHW�X����o�Yϸ�ӧc͚5(,,�_|�{�';�Y����������o����@ O<�>����,Dg�Ѵ�9m�<&I�F��z=ZZZP__�n*I�z`���oDQ� ��3w�\u��-[���?���3���������?k�Q.X����j4�gA4���X�����V�������$���	��׿P__�;v`׮]طov���ٌI�&aڴi0�p�݈�b���(--�W\ صk���K U�/Y����wQVV��'tf����O~��~����꫁ԉ�F��_=�O���������+���[�ٳG�_mm-�O����r���cɒ%�7o4��k��ٳq饗�����.,Z�s�΅��ľ}���O�4	:�n����<x�����믱g�����N??~�x�|>L�>W\qEƵ�{�B!�л���f��q�5�`ѢE���+��ёѓP[[������ǕW^���>�� ��+����ߏy��!����J����z��1�|L�6��gй��ĝ��r}0~�x,Y���r�Z��aɒ%X�p�q����"]G��UVV���
�&M��������؈����N;�e�p�C4�Ѵ�9m�  ��z+�~�i����|���0��={p�wd| ����ڵ?��� ���֭[�^x�]w�z�2R���U��y��w��0��%K`4�k�x~�!�nݪ~���n7�-[��/x��p�w@Q�b1���,_�\}���V���������y\v�ej�Q2��޽{��/�7��*++��~�r�- ��>�'NDss3֮]�_��Ӗ�{��˗����ƌ32�۳��F<�裰�l�=��(��}��׿V�a��U$�(��W�^����q�}������_�u�Y�˖-�}�ݧ��b1$�I�t������~�٬ޣ���ӟN��:38m�0�X,�x<Y����|��/_��b������W�3�|ݰa���D"���� ��z:�۷oGuu�qS��>u���އh(�i�s��JKKԆ���A}}=��볳��?�1���jh4lڴ	/��|>��ˏ[0y�d,\�F�]]]x������I�0o�<�����o����R�PWW�cǎe��a侾>l޼����$	�^{-f͚��1�͐$	����B���{Q[[�X,���z6l@4EMM���zl߾�D����?>�ϟ���R$	l߾G���#�ӽ�ޛ�(s�΅�fCOO�y�l޼�HS�LAmmmvv Ճ��ЀݻwCQ�l6|�����_�Y�fAQl۶�=�>����n�	3g��M7����h4�/����V�U�=y�d��?��lFcc#V�X��h4b��<yr�w!���\��Z-�^�Z���ٳgc����{������N�\�;v�V�ży�0�|���8v�/^�ŋ�`0���x�7PWW����XMMM���o3F:�e��h�}�N4*���V�Z��x��Ѐ�n�	�����&L��`@,�$I7n�:v�Ee4|kjj�p8  ;v��3�<��?��h�,C����;�DCC�:�m�ʕX�x�:�<P8Ɔ�x�bu��h4��/T�x<,]�555���[q�%�@E�B!8�\.��ah4\v�eؾ};|>,f̘�3f�b�������?<��Fz��/�8�#�J�&m�������x�'p�7b�޽�� G���?�U�V�[AAzzz0g�<��Chnn���߮.64������j |��7��{�f͚��kjj�r���	�S�LA? ��tbڴij^"�Ε� �b�ƍX�x1>��s �N�CMMJJJ�TYV[[EQ�(
�f3�L���۷cݺu�Bp:�p:��z�����͛7c���زe���W^yw�y琻�β�����}�Ntڤ�^�9s�`����$I���j^�ө.�� �D" '��NQ��'�;%�"�N��'�g�� `�Zq��7��oVkI��������ӦMôi� ZZZ�r�)|��_�|�EAyy9,X�5k�`�ƍ����� �Bػw/��ݫV���_~96n܈7�x��{/jjj2z������  �H ���� Y�5y�d,X�@m!�ߕ���� >���$I())Q� UUUa���1c��"��Ԣm۶e�hnn����}4Ng�l6�sz��5h��%�hM&R�PL�B!<��S����}�݇e˖��G�Ν;ռ---�"�t��,��h4H$ja�+�N��'������ꂲ����ǃ%K����>� �-[��{ �)Ceee����D��;���$I(..��hT���ر���ǵ�^��+W���� `	�;wnF�4�Á�s�b֬Yj�U ��ɓq饗"�����ԩS3v'���VG***0}�tL�>=c�E{{������;*++q뭷bٲex�G��o�y���q��C	�Bjy�k�.TVVb�ܹx�G����kV�X���*$	$�I���b�ʕYw�o�)�	5��Y6�3��&��O� �4:H7��`��݋��n ��_���flڴ);���+�|>Ȳ�����ذa֮]�5k��������_�#W_}5����?�!$I����W_}��$Z��-�����?����a���ܹ�h�?�06l؀�_~�W���ŋ �|�	�9�n�w��|��'ٷr|���6D"��z,]�����q�e�e�gӦMhnnƧ�~��F�<݋���t�^�ڵk�r��L&�����EQ��j��}��.�����544`߾}H&�(..�;＃x@��B������ �뮻�֭����f��Z�
S�NU���v.�C9t�z�ܤI��~�z�_������bΜ9X�|�:sǎؿ?DQ�7܀�� �r\�e<��S����ҥK3��v:��[���>DCI��s�io��!�������d�=�����ߏ��ף��Eͫ�h�(
��$��$E��ﾋ��z^��ƍ�̙3!�"��ك�{.�---x�W���
�N���*�Fx<���+�裏��-�L�?x8FGGA@WW֮]��{���}�u������G$�ĉ1e�(���?�\��---ضm�:�t۶m��x<�d򿛗)���;���غu+��8�z=4�L&�^�u�֡��&�	7�pƍ�ߏ�7⭷�R��@�(���d2����?������{�w� L�8��G�������9�^/�^/���K�q���`���hnnV���n����K/�4�H�{��� �;��|Y�z5���I�0k�,����k���q�m�A�ף��O>�$֬Y�cǎ�b����Cmm-�|�MlڴI��nj��,��>C�D��j��ݪ *++!Z[[���#��ӧ�j�b˖-��P���1q�D���� x(���[��nȲ��>�,;�fϞ ���PFz���j\t�Eؽ{����`�=:;;�\�7p��n�3g�DGG�qϬ��Fyy9�oߞ��QQQ�+V������x�G0o�<<��0�L'Ȧ�3a���թ=^t�2p�V:�T}0EEE�T���3]6�����EE���#�L���9;�8C������Cgg'�~v2�Y);x����``u�7f'�dCj_s��7��f�l6D"���x���?Bc��Ӄe3���f3


��v~0�N[	L&Sv�Y������8p� |>_v���҂��zu*Չx�駱u�V�b1����f�����/�+'"�����f��P�(�9�� �ÇglFDDcG���/:�eee@���\�*9�(��s��랈�Ʈp8���fDD�t����8`���S�:<+{�y""""":7ɲ��M2��ܑ<D�Q���j!""""�s[�m���7��	�H���D��鐟���LDDDDD���|�t:D"x<���a�< u2o2���b����N&""""�s����bA2�DWWWv�r
"��zT��f�&""""�s����f ��nD"��,#�)x@��t a�ZQ\\I����YD�$��+���Oxʐ�<E�eBE$	���j�6�V�������v#
eg�٨����ө�H����z����H$�����h`6�a�Z��j �@ ��x<;���P�f2�`�ۡ���k�`�P�H�X�� �<�G�A�(��j�����eyyyjz4Eoo/�@��N�Ii&�	f�9���F0���?eAC�)	�$I�,�0��t�j��h4!;+��d2�D"�X,�h4�p8�P(t�ӓ�rJ�"""""�rު�������o�����('�����('�����('�����('�����('�yN��ې�    IEND�B`�
```
```svg
<!-- 

MIT License

Copyright (c) 2025 JustStudio. <https://juststudio.is-a.dev/>

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.

\-->
<svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" viewBox="0 0 300 300" shape-rendering="geometricPrecision" text-rendering="geometricPrecision"><path xmlns="http://www.w3.org/2000/svg" d="M0,200 s248.9-.13 300-70" fill="none" stroke="#3f3f3f" stroke-width="2" stroke-linecap="round"/></svg>
```
```svg
<!-- 

MIT License

Copyright (c) 2025 JustStudio. <https://juststudio.is-a.dev/>

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.

\-->
<svg xmlns="http://www.w3.org/2000/svg" width="1024" height="1024" viewBox="0 0 1024 1024" version="1.1">
    <path d="M 518.656 198.677 C 518.404 198.929, 516.016 199.317, 513.349 199.538 C 506.674 200.092, 500.971 200.830, 498 201.525 C 496.625 201.847, 494.375 202.255, 493 202.432 C 488.792 202.974, 483.782 203.983, 477.500 205.555 C 469.302 207.605, 468.500 207.823, 468.500 208.006 C 468.500 208.093, 467.237 208.371, 465.694 208.624 C 463.331 209.011, 452.219 212.639, 447 214.727 C 446.175 215.057, 445.275 215.388, 445 215.463 C 442.343 216.183, 439.406 217.396, 439 217.941 C 438.725 218.310, 435.575 219.648, 432 220.915 C 423.517 223.922, 418.676 226.144, 417.765 227.448 C 417.361 228.027, 417.024 228.151, 417.015 227.724 C 416.995 226.714, 395.745 237.805, 394.773 239.333 C 394.365 239.975, 394.024 240.146, 394.015 239.714 C 394.007 239.282, 392.321 240.069, 390.268 241.464 C 388.215 242.859, 386.212 244, 385.816 244 C 384.771 244, 372.505 252.686, 371.016 254.480 C 369.412 256.413, 367.274 256.417, 365.348 254.491 C 364.518 253.661, 363.476 253.206, 363.033 253.480 C 362.589 253.754, 361.671 253.308, 360.991 252.489 C 360.311 251.670, 358.910 251, 357.878 251 C 356.845 251, 356 250.550, 356 250 C 356 249.450, 355.525 249, 354.944 249 C 354.363 249, 351.326 247.729, 348.194 246.175 C 345.062 244.621, 342.050 243.217, 341.500 243.055 C 340.950 242.893, 339.375 242.318, 338 241.778 C 327.899 237.809, 317.310 234.344, 316.342 234.691 C 315.705 234.919, 314.890 234.632, 314.533 234.053 C 314.175 233.474, 312.634 233, 311.108 233 C 309.582 233, 308.024 232.691, 307.646 232.313 C 307.268 231.935, 306.181 231.516, 305.230 231.382 C 304.278 231.248, 299.675 230.236, 295 229.133 C 290.325 228.031, 284.025 226.868, 281 226.548 C 277.975 226.228, 273.025 225.659, 270 225.283 C 252.363 223.091, 209.932 224.968, 197 228.513 C 196.725 228.588, 194.250 228.986, 191.500 229.397 C 188.750 229.808, 185.375 230.568, 184 231.086 C 182.625 231.603, 180.488 232.246, 179.250 232.513 C 170.497 234.407, 167.510 235.268, 166.250 236.260 C 165.563 236.802, 165 236.989, 165 236.676 C 165 236.363, 161.963 237.233, 158.250 238.609 C 154.538 239.986, 150.825 241.267, 150 241.457 C 147.263 242.086, 139.530 245.318, 137.527 246.669 C 136.443 247.401, 134.789 248, 133.852 248 C 131.586 248, 98 264.963, 98 266.107 C 98 266.598, 97.155 267, 96.122 267 C 95.090 267, 93.685 267.675, 93 268.500 C 92.315 269.325, 91.205 270, 90.533 270 C 89.860 270, 88.495 270.900, 87.500 272 C 86.505 273.100, 85.085 274, 84.345 274 C 83.605 274, 83 274.450, 83 275 C 83 275.550, 82.501 276, 81.891 276 C 79.879 276, 62 293.252, 62 295.193 C 62 295.715, 61.437 296.770, 60.750 297.537 C 60.062 298.303, 58.985 299.552, 58.355 300.311 C 56.590 302.438, 49.052 318.249, 48.729 320.500 C 48.571 321.600, 48.137 322.988, 47.765 323.584 C 47.392 324.180, 46.818 326.205, 46.488 328.084 C 46.159 329.963, 45.500 333.300, 45.022 335.500 C 42.724 346.102, 45.051 371.259, 49.057 379.112 C 50.126 381.207, 51 383.664, 51 384.572 C 51 388.475, 62.719 406.730, 69.448 413.306 C 74.672 418.413, 79.015 422, 79.973 422 C 80.538 422, 81 422.360, 81 422.801 C 81 423.640, 89.638 429.408, 90.185 428.933 C 90.358 428.783, 92.483 429.911, 94.907 431.441 C 97.330 432.971, 99.805 434.298, 100.407 434.390 C 101.008 434.481, 101.950 434.821, 102.500 435.145 C 110.803 440.033, 138.482 442.831, 152.255 440.174 C 156.691 439.318, 169.650 439.457, 176.500 440.434 C 178.150 440.670, 179.982 440.893, 180.570 440.931 C 182.470 441.053, 190.132 448.217, 191.108 450.783 C 192.858 455.388, 193.088 459.655, 193.517 495.642 C 193.752 515.364, 194.165 534.200, 194.434 537.500 C 194.703 540.800, 195.137 546.200, 195.400 549.500 C 195.662 552.800, 196.123 556.886, 196.423 558.581 C 196.723 560.276, 197.185 563.201, 197.450 565.081 C 197.714 566.961, 198.170 569.625, 198.464 571 C 198.757 572.375, 199.257 575.525, 199.576 578 C 200.060 581.763, 201.684 589.358, 203.424 596 C 203.640 596.825, 204.374 599.750, 205.054 602.500 C 205.735 605.250, 206.910 608.733, 207.666 610.240 C 208.422 611.746, 209.038 613.321, 209.035 613.740 C 209.020 615.802, 212.551 626, 213.281 626 C 213.741 626, 213.890 626.370, 213.610 626.822 C 213.331 627.274, 213.795 628.961, 214.641 630.572 C 215.487 632.182, 216.353 634.366, 216.566 635.424 C 217.569 640.418, 233.042 669.497, 240.107 679.663 C 241.698 681.953, 243 684.316, 243 684.913 C 243 685.511, 243.367 686, 243.815 686 C 244.263 686, 245.381 687.575, 246.299 689.500 C 247.217 691.425, 248.395 693, 248.916 693 C 249.438 693, 250.126 693.821, 250.444 694.824 C 250.762 695.827, 251.824 697.514, 252.803 698.574 C 253.782 699.633, 256.069 702.668, 257.886 705.318 C 259.703 707.967, 261.822 710.378, 262.595 710.675 C 263.368 710.971, 264 711.579, 264 712.025 C 264 713.441, 280.782 731.865, 289.877 740.434 C 292.834 743.220, 297.359 747.525, 299.932 750 C 306.218 756.047, 315.475 763.938, 319.874 767 C 321.850 768.375, 324.316 770.260, 325.355 771.188 C 328.588 774.077, 341.899 783, 342.977 783 C 343.539 783, 344 783.348, 344 783.773 C 344 784.198, 345.688 785.416, 347.750 786.481 C 349.813 787.545, 352.713 789.236, 354.196 790.238 C 356.992 792.127, 378.500 803.373, 378.500 802.946 C 378.500 802.813, 380.525 803.737, 383 805 C 385.475 806.263, 387.500 807.198, 387.500 807.078 C 387.500 806.958, 388.850 807.388, 390.500 808.034 C 398.667 811.233, 405.577 813.805, 406.250 813.897 C 406.663 813.954, 407.225 814.056, 407.500 814.125 C 407.775 814.194, 408.337 814.335, 408.750 814.438 C 409.163 814.542, 412.450 815.569, 416.056 816.721 C 419.661 817.872, 424.161 819.071, 426.056 819.383 C 427.950 819.696, 430.850 820.324, 432.500 820.778 C 434.150 821.232, 436.175 821.551, 437 821.486 C 437.825 821.421, 439.175 821.690, 440 822.083 C 440.825 822.477, 443.975 823.106, 447 823.482 C 479.004 827.453, 509.300 826.187, 542.500 819.491 C 543.600 819.269, 544.950 819.023, 545.500 818.945 C 546.050 818.867, 547.343 818.351, 548.374 817.800 C 549.405 817.248, 551.205 816.676, 552.374 816.528 C 558.106 815.803, 574.895 810.501, 583 806.857 C 584.375 806.238, 586.400 805.403, 587.500 805.001 C 592.414 803.203, 609.632 795.547, 610.851 794.617 C 611.594 794.051, 612.755 793.506, 613.432 793.407 C 614.108 793.308, 618 791.301, 622.081 788.947 C 626.161 786.593, 630.962 783.973, 632.750 783.125 C 634.538 782.277, 636 781.227, 636 780.792 C 636 780.356, 636.873 780, 637.941 780 C 639.009 780, 640.171 779.532, 640.525 778.959 C 640.879 778.387, 643.043 776.746, 645.334 775.312 C 647.625 773.879, 650.574 771.647, 651.886 770.353 C 654.887 767.394, 656.038 767.408, 660.531 770.456 C 662.521 771.807, 666.479 773.789, 669.325 774.860 C 672.171 775.931, 676.750 777.923, 679.500 779.288 C 682.250 780.652, 687.425 782.758, 691 783.967 C 694.575 785.176, 698.897 786.793, 700.604 787.560 C 703.908 789.045, 714.204 791.756, 720 792.666 C 721.925 792.968, 724.276 793.627, 725.224 794.129 C 726.172 794.631, 730.447 795.511, 734.724 796.083 C 739.001 796.655, 743.625 797.340, 745 797.606 C 750.749 798.715, 759.518 799.235, 776.500 799.471 C 799.268 799.789, 812.128 798.661, 831.500 794.650 C 832.600 794.422, 835.410 793.919, 837.745 793.531 C 840.079 793.143, 842.248 792.408, 842.564 791.897 C 842.880 791.386, 843.489 791.184, 843.918 791.449 C 844.582 791.860, 858.409 788.125, 863 786.296 C 863.825 785.967, 864.950 785.597, 865.500 785.474 C 867.450 785.037, 876.544 781.836, 879.500 780.546 C 882.252 779.345, 887.500 777.118, 892.500 775.029 C 900.922 771.510, 917 763.451, 917 762.749 C 917 762.272, 917.414 762.138, 917.919 762.450 C 918.425 762.763, 919.438 762.451, 920.169 761.758 C 920.901 761.065, 923.975 759.200, 927 757.612 C 932.504 754.724, 941.704 748.953, 945.440 746.046 C 949.684 742.744, 957.972 734.449, 957.985 733.491 C 957.993 732.935, 958.900 731.919, 960 731.232 C 961.100 730.545, 962 729.378, 962 728.638 C 962 727.898, 963.090 726.116, 964.422 724.678 C 968.304 720.490, 976.276 702.756, 977.539 695.500 C 979.207 685.914, 979.659 680.484, 979.480 672.167 C 979.246 661.296, 978.304 653.436, 976.926 650.863 C 976.417 649.910, 976 648.387, 976 647.478 C 976 644.894, 969.226 630.091, 965.256 624 C 955.874 609.605, 929.667 589, 920.742 589 C 920.004 589, 918.973 588.575, 918.450 588.055 C 917.928 587.536, 916.061 586.859, 914.301 586.550 C 912.542 586.242, 909.998 585.570, 908.648 585.056 C 902.810 582.837, 884.480 582.241, 872.620 583.885 C 851.766 586.777, 834.628 581.777, 832.550 572.194 C 832.229 570.712, 831.721 568.375, 831.422 567 C 830.965 564.900, 829.683 500.778, 829.913 491.500 C 830.055 485.772, 825.439 448.343, 823.873 442.529 C 823.279 440.324, 822.649 437.390, 822.472 436.010 C 822.114 433.212, 818.282 418.339, 817.317 416 C 816.977 415.175, 816.591 414.050, 816.460 413.500 C 815.768 410.596, 811.657 399.237, 810.461 396.925 C 809.701 395.456, 809.362 393.972, 809.707 393.627 C 810.051 393.282, 809.883 393, 809.333 393 C 808.783 393, 808.333 392.367, 808.333 391.594 C 808.333 390.304, 805.895 384.797, 800.765 374.500 C 793.332 359.580, 789.947 353.455, 784.817 345.646 C 781.617 340.776, 779 336.581, 779 336.323 C 779 335.758, 768.812 322.352, 766.164 319.431 C 765.132 318.294, 764.110 316.876, 763.894 316.282 C 762.158 311.520, 729.615 278.189, 714 265.182 C 709.325 261.288, 703.870 256.728, 701.878 255.051 C 699.887 253.373, 697.787 252, 697.212 252 C 696.637 252, 696.017 251.606, 695.833 251.126 C 695.430 250.069, 680.296 239.716, 675.746 237.384 C 673.961 236.469, 672.275 235.413, 672 235.036 C 671.725 234.659, 669.475 233.316, 667 232.050 C 664.525 230.785, 661.825 229.374, 661 228.914 C 655.177 225.668, 642 219.151, 642 219.517 C 642 219.764, 640.399 219.067, 638.443 217.968 C 636.486 216.869, 634.124 215.842, 633.193 215.686 C 632.262 215.530, 630.600 214.928, 629.500 214.349 C 624.814 211.881, 597.497 204.064, 587.500 202.332 C 578.874 200.836, 571.669 199.787, 567 199.347 C 559.665 198.655, 519.238 198.095, 518.656 198.677" stroke="none" fill="#6c3cf4" fill-rule="evenodd"/>
</svg>
```
```justc

```
```md
# This is a test page
Generated using [Just an Ultimate Site Tool](https://just.is-a.dev/).

Some **bold**, *italic*, ***important***, __underlined__, ~~strikethrough~~, ~sub~, ^super^, ==marked==, __***very important***__, __==***extreme important***==__, ~~***not important***~~ text.
Some `code`; **`bold`**, *`italic`*, ***`important`***, __`underlined`__, ~~`strikethrough`~~, ~`sub`~, ^`super`^, ==`marked`==, __***`very important`***__, __==***`extreme important`***==__, ~~***`not important`***~~ code.

1. List
2. (with numbers)

- List
- (no numbers)

A line:
---

\`\`\`
Some code here
\`\`\`

# Blockquotes test
> A blockquote.
> > Another one!
> > > And another blockquote!
> > > > Many nested blockquotes.
> > > > > This line should not be another nested blockquote. (Limit: 4 nested blockqutes)

> [!NOTE] A note!
> [!TIP] A tip!
> [!IMPORTANT] Something important.
> [!WARNING] A warning!
> [!CAUTION] Another warning?

> [!NOTE] `[!NOTE]`, `[!TIP]`, `[!IMPORTANT]`, `[!WARNING]` and `[!CAUTION]` are should be in one line. You can add \<br> tags to break the line for HTML. <br> > `[!NOTE]`, `[!TIP]`, `[!IMPORTANT]`, `[!WARNING]` and `[!CAUTION]` cannot have nested blockquotes.


> Multi <br>Line <br>(\<br>)

> Multi
> 
> Line
>
> (\\n + > + space + \\n)

> Multi
>
> Line
>
> (\\n + > + \\n)

# Headers test (h1)
## h2
### h3
#### h4
##### h5
###### h6
> [!IMPORTANT] Only h1, h2, h3 and h4 will be included in the "On this page" content list and will have their own unique IDs for shortlinks.

# Escape test
Not a line:
\---

\1. Not a list
2\. (with numbers)

\- Not a list
\- (no numbers)

\`\`\`
No code here
\`\`\`

\> Not a blockquote.

> \[!NOTE] A blockquote, but not a note.

\https://example.com - escaped link
`https://example.com` - disabled auto linking

# Links test
[a link](https://just.is-a.dev/ "link title")
<https://just.is-a.dev/>
https://just.is-a.dev

---

## v0.0.28 bugs:
#### blockquote link:
> [!WARNING] Just an Ultimate Site Tool is in **beta**. Expect regular updates, possible bugs, and changes. If you have found a bug, please [report it here](https://github.com/js-just/_just/issues/new?labels=bug&template=bug.md).

#### text styling:
"**`_just`**"

## v0.0.29 features:
-# small text

_just: prev: /docs/getting-started
_just: next: /docs/getting-started

```
### test
```js
/*

MIT License

Copyright (c) 2025 JustStudio. <https://juststudio.is-a.dev/>

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.

*/

const box = document.querySelector('.pjs');
const processor = document.querySelector('.p');
const process = processor.querySelector('.l');
const inputs = [
    processor.querySelector('.c'),
    processor.querySelector('.tl'),
    processor.querySelector('.bl'),
];
const outputs = [
    processor.querySelector('.r'),
    processor.querySelector('.d')
];
const label = processor.querySelector('span');

/**
 * @returns {[HTMLDivElement, {x: number, y: number}, number]}
 */
function centerDot() {
    return [
        document.createElement('div'),
        {x: window.innerWidth, y: window.innerHeight},
        processor.offsetHeight / 2 + 4,
    ]
}

/**
 * @param {HTMLElement} track 
 * @param {number} offset 
 * @returns {string}
 */
function yPos(track, offset) {
    return `${(track.offsetTop + track.offsetHeight / 2 + 1) - offset - 1.5 + processor.offsetTop + 55}px`;
}

/**
 * @param {String} c 
 * @param {String} t1 
 * @param {String?} t2 
 */
function centerInput(c, t1, t2) {
    const [element, screen, offset] = centerDot();
    const y = yPos(inputs[0], offset);
    element.style.backgroundColor = c;
    element.style.boxShadow = `0px 0px 3px ${c}`;
    element.style.translate = `-${screen.x / 4}px ${y}`;
    box.appendChild(element);
    setTimeout(()=>{
        element.style.translate = `${screen.x / 4}px ${y}`;
    }, 100);
    const span = document.createElement('span');
    span.innerText = t1;
    span.style.opacity = '0';
    element.appendChild(span);
    let time = -500;
    setTimeout(()=>{
        span.style.opacity = '1';
    },1000+time);
    setTimeout(()=>{
        span.style.opacity = '0';
    },3000+time);
    if (t2) {
        setTimeout(()=>{
            span.innerText = t2;
            span.style.opacity = '1';
        },3100+time);
        setTimeout(()=>{
            span.style.opacity = '0';
        },5100+time);
        time += 2100;
    }
    setTimeout(()=>{
        element.style.translate = `${screen.x / 4 * 3}px ${y}`;
    },3200+time);
    setTimeout(()=>{
        element.remove();
        process.style.borderColor = '#6e3bf3';
        process.style.filter = 'drop-shadow(0px 0px 8px #6e3bf3)';
    },3450+time);
}

let _outputs = 0;
/**
 * @param {String} c 
 * @param {String} t1 
 * @param {String?} t2 
 */
function output(c, t1, t2) {
    _outputs++;
    const offset2 = ((_outputs % 2 == 0 ? _outputs : -_outputs) - 1) * 35 - (_outputs % 2 == 0 ? 35 : 0);
    const [element, screen, offset] = centerDot();
    const y = yPos(inputs[0], offset);
    element.style.backgroundColor = 'transparent';
    element.style.translate = `${screen.x / 4}px ${y}`;
    box.appendChild(element);
    setTimeout(()=>{
        element.style.translate = `${screen.x / 4 * 3 + offset2}px ${y}`;
    }, 100);
    setTimeout(()=>{
        process.style.borderColor = '#3f3f3f';
        process.style.filter = 'none';
        element.style.backgroundColor = c;
        element.style.boxShadow = `0px 0px 3px ${c}`;
    }, 200);
    const span = document.createElement('span');
    span.innerText = t1;
    span.style.opacity = '0';
    element.appendChild(span);
    let time = -500;
    setTimeout(()=>{
        span.style.opacity = '1';
    },1000+time);
    setTimeout(()=>{
        span.style.opacity = '0';
    },3000+time);
    if (t2) {
        setTimeout(()=>{
            span.innerText = t2;
            span.style.opacity = '1';
        },3100+time);
        setTimeout(()=>{
            span.style.opacity = '0';
        },5100+time);
        time += 2100;
    }
    setTimeout(()=>{
        element.style.translate = `${screen.x / 4 * 5}px ${y}`;
    },3200+time);
    setTimeout(()=>{
        element.remove();
        _outputs--;
    },3450+time);
}

let _labelAnim;
function labelAnim(switch_) {
    const s = '&nbsp;';
    const d = '.';
    switch(switch_) {
        case true:
            _labelAnim = setInterval(()=>{
                switch(label.innerHTML) {
                    case d+s.repeat(2):
                        label.innerHTML = d.repeat(2)+s;
                        break;
                    case d.repeat(2)+s:
                        label.innerHTML = d.repeat(3);
                        break;
                    case d.repeat(3):
                        label.innerHTML = s+d.repeat(2);
                        break;
                    case s+d.repeat(2):
                        label.innerHTML = s.repeat(2)+d;
                        break;
                    case s.repeat(2)+d:
                        label.innerHTML = s.repeat(3);
                        break;
                    default:
                        label.innerHTML = d+s.repeat(2);
                        break;
                }
            },200);
            break;
        default:
            clearInterval(_labelAnim);
            label.innerHTML = s.repeat(3);
            break;
    }
};

const shuffleArray = function (array) {
    for (let i = array.length - 1; i > 0; i--) {
        const j = Math.floor(Math.random() * (i + 1));
        [array[i], array[j]] = [array[j], array[i]];
    }
    return array;
};
function animateTyping(elementId, text, speed = 100, callback = null) {
    const element = document.getElementById(elementId);
    if (!element) {
        return;
    };
    let index = 0;
    element.innerHTML = '';
    function type() {
        if (index >= text.length) {
            if (element.innerHTML !== text.replaceAll('\n', '<br>')) {
                console.warn(`"${element.innerHTML}" !== "${text.replaceAll('\n', '<br>')}"`)
            };
            if (callback) callback();
            return;
        };
        if (text.charAt(index) === '<') {
            let endIdx = -1;
            const openTagMatch = text.substring(index).match(/^<([a-zA-Z0-9]+)[^>]*>/);
            if (openTagMatch) {
                const tagName = openTagMatch[1];
                const closeTagStr = `</${tagName}>`;
                const closeIdx = text.indexOf(closeTagStr, index);
                if (closeIdx !== -1) {
                    endIdx = closeIdx + closeTagStr.length;
                    const fullTagBlock = text.substring(index, endIdx);
                    element.innerHTML += fullTagBlock;
                    index = endIdx;
                } else {
                    element.innerHTML += '<';
                    index++;
                }
            } else {
                element.innerHTML += '<';
                index++;
            }
        } else {
            element.innerHTML += text.charAt(index);
            index++;
        };
        element.innerHTML = element.innerHTML.replaceAll('\n', '<br>');
        setTimeout(type, speed);
    };
    if (speed === 0) {
        index = text.length + 1;
        element.innerHTML = text;
    };
    type();
};

function time(ms) {
    return ms > 999 ? `${Math.floor(ms/100)/10}s` : `${ms}ms`;
};

let canAnimate = true;

function compressor() {
    canAnimate = false;
    const variations = [
        ['white', '.html', '2kB', '0.9kB'],
        ['white', '.html', '14kB', '0.5kB'],
        ['white', '.html', '25kB', '6.8kB'],
        ['white', '.html', '18kB', '5.6kB'],
        ['white', '.js', '7kB', '0.8kB'],
        ['white', '.js', '15kB', '3.2kB'],
        ['white', '.js', '25kB', '6.1kB'],
        ['white', '.js', '20kB', '5.9kB'],
        ['white', '.css', '10kB', '2kB'],
        ['white', '.css', '22kB', '4.2kB'],
        ['white', '.css', '19kB', '2.6kB'],
        ['white', '.css', '24kB', '4.2kB'],
    ];
    const data = shuffleArray(variations)[0];
    const offset = Math.ceil((Math.random() / 2 + 0.700) * 1000);
    labelAnim();
    label.id = `c${data[1]}${offset}`;
    animateTyping(label.id, 'Compressor', 75);
    centerInput(data[0], data[1], data[2]);
    setTimeout(()=>{
        labelAnim(true)
    }, 5050);
    setTimeout(()=>{
        labelAnim();
        output(data[0], data[1], data[3]);
        animateTyping(label.id, `Compressing completed (${time(offset)})`, 50, ()=>{
            setTimeout(()=>{
                labelAnim();
                canAnimate = true
            },1200)
        })
    }, 5050+offset+1200);
}

function redirector() {
    canAnimate = false;
    const offset = Math.ceil((Math.random() / 2 + 0.700) * 1000);
    labelAnim();
    label.id = `r${offset}`;
    animateTyping(label.id, 'Redirector', 75);
    centerInput('white', 'just.config.js');
    setTimeout(()=>{
        labelAnim(true)
    }, 2950);
    setTimeout(()=>{
        labelAnim();
        output('white', '.css');
        output('white', '.js');
        output('white', '.html');
        animateTyping(label.id, `Generating completed (${time(offset)})`, 50, ()=>{
            setTimeout(()=>{
                labelAnim();
                canAnimate = true
            },1200)
        })
    }, 2950+offset+1200);
}

function generator() {
    canAnimate = false;
    const offset = Math.ceil((Math.random() * 2 + 1.700) * 1000);
    labelAnim();
    label.id = `g${offset}`;
    animateTyping(label.id, 'Generator', 75);
    centerInput('white', '.md');
    setTimeout(()=>{
        labelAnim(true)
    }, 2950);
    setTimeout(()=>{
        labelAnim();
        output('white', '.css');
        output('white', '.js');
        output('white', '.html');
        animateTyping(label.id, `Generating completed (${time(offset)})`, 50, ()=>{
            setTimeout(()=>{
                labelAnim();
                canAnimate = true
            },1200)
        })
    }, 2950+offset+1200);
}

let lastanimation = undefined;
function animate() {
    labelAnim();
    setInterval(()=>{
        if (canAnimate) {
            canAnimate = false;
            const animation = shuffleArray([compressor, redirector, generator].filter(anim != lastanimation))[0];
            lastanimation = animation;
            setTimeout(animation, 500)
        }
    },100)
};
animate()

```
```js
/*

MIT License

Copyright (c) 2025 JustStudio. <https://juststudio.is-a.dev/>

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.

*/

const APIURL = 'https://test.just.is-a.dev/data/codes.json';
const none = 'none';
const entr = 'Enter the code or command, or type "help" and press "Enter"...';
let cooldown = false;
let loadingerr = false;
let aTerr = false;
/**
 * @param {string} elementId 
 * @param {string} text 
 * @param {number?} speed 
 * @param {Function?} callback 
 * @returns {void}
 */
function animateTyping(elementId, text, speed = 100, callback = null) {
    const element = document.getElementById(elementId);
    if (!element || (text === entr && cooldown)) {
        return;
    };
    cooldown = true;
    let index = 0;
    element.innerHTML = '';
    function type() {
        if (index >= text.length) {
            cooldown = false;
            if (element.innerHTML !== text.replaceAll('\n', '<br>')) {
                aTerr = true;
                console.warn(`"${element.innerHTML}" !== "${text.replaceAll('\n', '<br>')}"`)
            };
            if (callback) callback();
            return;
        };
        if (text.charAt(index) === '<') {
            let endIdx = -1;
            const openTagMatch = text.substring(index).match(/^<([a-zA-Z0-9]+)[^>]*>/);
            if (openTagMatch) {
                const tagName = openTagMatch[1];
                const closeTagStr = `</${tagName}>`;
                const closeIdx = text.indexOf(closeTagStr, index);
                if (closeIdx !== -1) {
                    endIdx = closeIdx + closeTagStr.length;
                    const fullTagBlock = text.substring(index, endIdx);
                    element.innerHTML += fullTagBlock;
                    index = endIdx;
                } else {
                    element.innerHTML += '<';
                    index++;
                }
            } else {
                element.innerHTML += '<';
                index++;
            }
        } else {
            element.innerHTML += text.charAt(index);
            index++;
        };
        element.innerHTML = element.innerHTML.replaceAll('\n', '<br>');
        setTimeout(type, speed);
    };
    if (speed === 0) {
        index = text.length + 1;
        element.innerHTML = text;
    };
    type();
};
function checkFirstLetterCase(text) {
    if (!text || typeof text !== 'string') {
        return undefined;
    }
    const firstChar = text.charAt(0);
    if (firstChar === firstChar.toUpperCase() && firstChar !== firstChar.toLowerCase()) {
        return true;
    } else if (firstChar === firstChar.toLowerCase() && firstChar !== firstChar.toUpperCase()) {
        return false;
    } else {
        return null;
    }
};
function exitFullscreen() {
    if (document.fullscreenElement) {
        document.exitFullscreen().catch(e=>{console.warn(e)})
    }
};

(async()=>{
    /**
     * @returns {Promise<{data:any[],nums:any[]}>}
     */
    async function getCodes() {
        const responce = await fetch(APIURL).then((r)=>{
            return r.json();
        }).catch((_e)=>{loadingerr=true});
        let[data,nums]=[[],[]];
        for (const[key,val]of Object.entries(responce)) {
            if (key !== 'README') {
                val.forEach((item)=>{
                    data.push(item);
                    nums.push(item.code);
                });
            }
        };
        data = data.filter(item=>item.data);
        return {
            data,nums:nums.filter((item)=>{
                let output = false;
                data.forEach((code)=>{
                    output=!output?code.code===item:output;
                });
                return output;
            })
        }
    }
    /**
     * @param {text} code 
     * @param {any[]} data 
     * @returns {{code: String, message: String, crashed: Boolean, data?: {mg: boolean, i: string | null}} | null}
     */
    function getCodeData(code, data) {
        let output = null;
        data.forEach((item)=>{
            if (item.code === code) {
                output = item;
            }
        });
        return output;
    }

    const params = new URLSearchParams(window.location.search);
    const code = params.get('c');
    const init = params.get('i') === 'y';
    const codes = await getCodes();

    const elem = (id) => document.getElementById(id);
    elem('e').style.display = none;
    function redirect(to) {
        try{window.location.replace(to)}catch(e){};try{window.location.href=to}catch(e){};try{window.location.assign(to)}catch(e){}
    }
    const redirecting = (to) => `Redirecting to "<a href="${to}" target="_self">${to}</a>"...`;
    function close_() {
        const url_ = 'https://just.is-a.dev/';
        exitFullscreen();
        elem('d').innerHTML = redirecting(to);
        redirect(url_)
    };
    const closecmds = [
        'kill', 'exit', 'home', 'e'
    ];
    const yescmds = [
        'y', 'yes', 'ye', 'yeah', 'yep', 'sure', 'ok', 'k'
    ];
    function disableD() {
        const f = elem('d').cloneNode();
        f.id = 'f';
        elem('d').after(f);
        elem('d').style.display = none;
    };
    function enableD() {
        elem('f')?.remove();
        elem('d').style.display = null;
        animateTyping('d', entr);
    };
    async function codecmd(cmd) {
        const codess=await getCodes();
        if (codess.nums.includes(cmd)) {
            const url_ = `?c=${cmd}&i=y`;
            elem('f').innerHTML = redirecting(`https://just.is-a.dev/code${url_}`);
            window.location.search = url_
        } else {
            disableD();
            elem('f').innerText = 'No code found and unknown command.';
            setTimeout(enableD, 1000)
        }
    };
    const helpcmds = [
        'help', 'h'
    ];
    const listcmds = [
        'list', 'l'
    ];
    function timeoutED() {
        setTimeout(enableD, 3000)
    };
    function fatal(err) {
        elem('loader').classList.add('fatal');
        elem('loader').innerText = err;
        elem('a')?.remove();
        elem('b')?.remove();
        elem('c')?.remove();
        animateTyping('d', 'Press any key to retry...', 25, ()=>{
            window.addEventListener('keydown', ()=>{
                exitFullscreen();
                elem('d').innerHTML = 'Reloading window... <small>The window didn\'t reload? Check your internet connection and try to reload the window manually.</small>';
                window.location.reload()
            })
        });
        throw new Error(err)
    }
    function animErr() {
        if (aTerr) {
            fatal('Unexpected behavior')
        }
    }
    function helpcmd() {
        animErr();
        disableD();
        animateTyping('f', '<strong>Command list:</strong>\nhelp - help command / command list\nhome - redirect to home page\nlist - list of codes\nexit - exit this terminal, same as <code>home</code> command', 30, timeoutED)
    };
    function listcmd() {
        animErr();
        disableD();
        animateTyping('f', `<strong>List of codes:</strong>\n${
            codes.nums.sort((a,b)=>{
                a = parseInt(a);
                b = parseInt(b);
                if (isNaN(a) || isNaN(b)) {
                    return -1
                };
                return a > b ? 1 : -1
            }).join('\n')
        }`, 40, timeoutED)
    };
    let interval;
    let enterKeyCooldown = false;
    let listener;
    let aEEid=0;
    /**
     * @param {Function} oncommand 
     * @param {boolean?} onlyYorN
     */
    function animElemE(oncommand, onlyYorN = false) {
        const runid = aEEid++;
        animErr();
        if (interval) clearInterval(interval);
        interval = setInterval(()=>{
            elem('e').style.display = elem('e').style.display === none ? null : none
        }, 500);
        let input = '';
        function updInp() {
            animErr();
            if (input === '') {
                elem('text')?.remove();
                elem('e').insertAdjacentHTML('beforebegin', '<span id="text"></span>');
            } else if (elem('text')) {
                elem('text').innerText = `${input}`;
            } else {
                elem('e').insertAdjacentHTML('beforebegin', `<span id="text">${input}</span>`);
            }
        }
        const keydownListener=(event)=>{
            if (aTerr) {
                animErr();
            } else if (runid === aEEid - 1) {
                if ((event.key.toLowerCase() === 'c' || event.key.toLowerCase() === 'd') && event.ctrlKey) {
                    event.preventDefault();
                    close_()
                } else if (/^[a-zA-Z0-9]$/.test(event.key) && !event.ctrlKey) {
                    event.preventDefault();
                    input += event.key;
                    updInp()
                } else if (event.key.toLowerCase() === 'Enter'.toLowerCase() && !enterKeyCooldown) {
                    event.preventDefault();
                    enterKeyCooldown = true;
                    const uncooldown=()=>{setTimeout(()=>{enterKeyCooldown=false},300)};
                    const inpt = input.trim().toLowerCase();
                    input = '';
                    updInp();
                    if (closecmds.includes(inpt) && !onlyYorN) {
                        close_();
                        uncooldown()
                    } else if (onlyYorN) {
                        if (yescmds.includes(inpt)) {
                            oncommand();
                            uncooldown()
                        } else {
                            animateTyping('d', entr, 25, () => {
                                animElemE((cmd)=>{codecmd(cmd);uncooldown()});
                            })
                        }
                    } else if (helpcmds.includes(inpt)) {
                        helpcmd();
                        uncooldown()
                    } else if (listcmds.includes(inpt)) {
                        listcmd();
                        uncooldown()
                    } else {
                        oncommand(inpt);
                        uncooldown()
                    };
                    return
                } else if (event.key.toLowerCase() === 'Backspace'.toLowerCase()) {
                    event.preventDefault();
                    input = input.slice(0,-1);
                    updInp()
                }
            }
        };
        window.removeEventListener('keydown',listener);
        listener=keydownListener;
        window.addEventListener('keydown',keydownListener)
    };
    animateTyping('loader', `<small>Initializing</small> Just an Ultimate Site Tool helper terminal <small>...</small>\n${' '.repeat(20)}\n${loadingerr ? 'Error' : 'Done.'}`, init ? 0 : 50, ()=>{
        setTimeout(()=>{
            if (code != null && codes.nums.includes(code) && !loadingerr) {
                elem('loader').innerText = `> ${code}\n\n`;
                const codedata = getCodeData(code, codes.data);
                if (codedata.crashed || code.startsWith('03')) {
                    elem('a').classList.add('error');
                } else if (code.startsWith('02')) {
                    elem('a').classList.add('warn');
                } else {
                    elem('a').classList.add('ok');
                };
                const info = codedata.data.i||'';
                const check = checkFirstLetterCase(info);
                animateTyping('a', code, 200, ()=>{
                    animateTyping('b', !codedata.data.mg?codedata.message:'', 50, ()=>{
                        if (codedata.data.mg) {
                            elem('b').remove();
                        };
                        if (check===true) {
                            elem('c').classList.add('info');
                        } else {
                            elem('c').classList.add('tip');
                        };
                        animateTyping('c', check===false?`To fix it, ${info}.`:check===true?info:''||'', 50, ()=>{
                            animateTyping('d', 'Do you want to redirect to the docs? (y/n)', 25, ()=>{
                                animElemE(()=>{
                                    const url_ = 'https://just.is-a.dev/docs';
                                    elem('d').innerHTML = redirecting(url_);
                                    redirect(url_)
                                }, true);
                            });
                        });
                    });
                });
            } else if (loadingerr) {
                fatal('Failed to fetch codes')
            } else {
                elem('loader').remove();
                elem('a').remove();
                elem('b').remove();
                elem('c').remove();
                animateTyping('d', entr, 25, ()=>{
                    animElemE(codecmd);
                })
            }
        }, init ? 0 : 234)
    });
    window.addEventListener('keydown',(event)=>{
        if (event.key.toLowerCase()==='Enter'.toLowerCase()) {
            setTimeout(()=>{enterKeyCooldown=false},350)
        } else if (event.key.toLowerCase()==='F11'.toLowerCase()) {
            event.preventDefault();
        }
    });
    setTimeout(()=>{
        if (document.fullscreenEnabled && document.fullscreenElement !== document.documentElement) {
            document.documentElement.requestFullscreen().catch(e=>{console.warn(e)})
        }
    }, init ? 0 : 555);
})();

```
```md
_just: title: Syntax highlighting test
# Syntax highlighting
\`\`\`json
{
    "hello": "world",
    "number": 1234567890,
    "array": [null, true, false]
}
\`\`\`
\`\`\`json
[
    "hello", "world"
]
\`\`\`
\`\`\`js
import {abc as cba} from '../test.js';
const abc = require('../../test.js');

class test {
    constructor () {
        return true
    }
}

console.log('hello world');
console.warn(1 + 1);
alert(false);
const abc = true;
for (i = 1; i <= 4; i++) {
    // do something
};
switch (abc) {
    case true:
        window.location.replace('https://juststudio.is-a.dev/');
        break;
    default:
        document.body.classList.add('a');
        break;
}
/**
 * @param {number} b
 * @returns {number}
 */
function a (b) {
    return b + 1;
}
let c = (d) => {
    return a(d) / 2;
}
var e = (f) => f + f;
if (typeof abc != 'boolean') {
    throw new Error('error');
} else if (.2 != 0.2) {
    console.error('what');
} else {
    try {
        new test();
    } catch (_ee) {
        const fewuhfuiwfuiweifuweiewhfiew = globalThis.localStorage.getItem('KEY');
        console.log(fewuhfuiwfuiweifuweiewhfiew);
    } finally {
        fetch();
    }
}
\`\`\`
\`\`\`html
<!DOCTYPE html>
<span class="hw" id="abc">Hello World!</span>
\`\`\`
\`\`\`css
* {
    background-color: black;
}
span {
    color: white;
}
.hw {
    border: 1px solid #6e3bf3;
}
#abc {
    -webkit-filter: blur(8em);
}
div:before {
    content: 'hello';
}
:root {
    --color: #ffffff;
    @media(max-width: 5px) {
        --color: #000000;
    }
}
::-webkit-scrollbar {
    width: 7px;
    height: 7px
}
\`\`\`
\`\`\`sh
#!/bin/bash
echo "Hello World!"
\`\`\`
\`\`\`py
#!/usr/bin/env python3
import time
out = int(time.time() * 1000)
print(out)
\`\`\`
\`\`\`lua
print("hiii")
warn(1 + 1)
local abc = true
while wait(1) do
    -- do something
end
\`\`\`
\`\`\`md
# markdown inside markdown
\`\`\`
\`\`\`go
// go
\`\`\`
\`\`\`golang
// golang
\`\`\`
\`\`\`golo

\`\`\`
\`\`\`gololang

\`\`\`
\`\`\`diff
  text
+ added
- removed
\`\`\`

```
```md
_just: title: Mattcone's Markdown Test
# Markdown test

This is a Markdown test for the Markdown Guide tools directory.

---

headings

# One

## Two

### Three

#### Four

##### Five

###### Six

Alternate One
=============

Alternate Two
-------------

---

paragraphs

first paragraph

second para

---

line breaks

line one with trailing whitespace  
line two right under

line one with no trailing whitespace, just hard return
line two right under

line one with no trailing backslash \
line two right under

---

bold

**asterisks**

__underscores__

in**middle**here

---

italic

*asterisk*

_underscore_

in*middle*here

---

bold and italic

***asterisks***

___underscores___

__*combo*__

**_second combo_**

---

blockquotes

> single

> multi
>
> line

> nested
>
>> blockquotes

---

ordered lists

1. first
2. second
3. third

1. this
2. is
    1. nested
3. now

---

unordered lists

- dashes
- here
  - nested

* asterisks
* here

+ plus
+ signs

---

code

`one tick mark`

    <one tab>
      <indented>

---

horizontal rules

throughout this :)

---

links

normal link => [cnn](https://cnn.com)

brackets => <https://cnn.com>

brackets => <me@somewhere.com>

naked url (test auto link) => https://cnn.com

---

images

![test image](https://www.markdownguide.org/assets/images/tools/joplin.png)

---

## Tables

| Syntax      | Description |
| ----------- | ----------- |
| Header      | Title       |
| Paragraph   | Text        |

---

## Fenced code blocks

\`\`\`
{
  "firstName": "John",
  "lastName": "Smith",
  "age": 25
}
\`\`\`

---

## Syntax highlighting

\`\`\`json
{
  "firstName": "John",
  "lastName": "Smith",
  "age": 25
}
\`\`\`

---

## Footnotes

Here's a simple footnote,[^1] and here's a longer one.[^bignote]

[^1]: This is the first footnote.

[^bignote]: Here's one with multiple paragraphs and code.

    Indent paragraphs to include them in the footnote.

    `{ my code }`

    Add as many paragraphs as you like.

---

## Heading IDs

### My Great Heading {#custom-id}

---

## Definition lists

First Term
: This is the definition of the first term.

Second Term
: This is one definition of the second term.
: This is another definition of the second term.

---

## Strikethrough

~~two tilde~~

~one tilde~

---

## Task lists

- [x] Write the press release
- [ ] Update the website
- [ ] Contact the media

---

## Emoji

copy and paste: ☕
shortcodes: :joy:

---

## Highlight

==twoequals==

::twohypens::

---

## Subscript

H~2~O

---

## Superscript

X^2^

---

## Abbreviation

*[HTML]: Hyper Text Markup Language

The HTML specification is maintained by the W3C.

---

## HTML

<em>italic test</em>

<strong>bold test</strong>
```
```md
test

```
### test
```json
{"$id":"https://just.is-a.dev/schema/r.json","$schema":"http://json-schema.org/draft-04/schema#","description":"_just just.config.js module.exports Redirector mode","type":"object","properties":{"type":{"type":"string"},"redirect_config":{"type":"object","properties":{"url":{"type":"string"},"params":{"type":"object","properties":{"title":{"type":"string"},"description":{"type":"string"},"keywords":{"type":"string"},"htmlLang":{"type":"string"},"robots":{"type":"string"},"charset":{"type":"string"},"viewport":{"type":"string"},"yandex":{"type":"string"},"google":{"type":"string"},"googleAnalytics":{"type":"string"},"content":{"type":"object","properties":{"text1":{"type":"string"},"text2":{"type":"string"},"text3":{"type":"string"}},"required":[]},"og":{"type":"object","properties":{"title":{"type":"string"},"description":{"type":"string"}},"required":[]},"twitter":{"type":"object","properties":{"card":{"type":"string"}},"required":["card"]}},"required":[]},"paths":{"type":"array","items":[{"type":"object","properties":{"path_":{"type":"string"},"url":{"type":"string"},"params":{"type":"object","properties":{"title":{"type":"string"},"description":{"type":"string"},"keywords":{"type":"string"},"htmlLang":{"type":"string"},"og":{"type":"object","properties":{"title":{"type":"string"},"description":{"type":"string"}},"required":[]},"twitter":{"type":"object","properties":{"card":{"type":"string"}},"required":["card"]}},"required":[]}},"required":["path_","url"]}]}},"required":["url"]}},"required":["type","redirect_config"]}
```
```webmanifest
{"name":"","short_name":"","icons":[{"src":"/android-chrome-192x192.png","sizes":"192x192","type":"image/png"},{"src":"/android-chrome-512x512.png","sizes":"512x512","type":"image/png"}],"theme_color":"#ffffff","background_color":"#ffffff","display":"standalone"}
```
