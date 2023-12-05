

### FUCK JAVASCRIPT, IT'S A ASS LANGUAGE


Use Tampermonkey

- go in pages with the gems
- wait until page is loaded
- press NUMPAD 5 (or change the keycode in the script to your liking)
- once finish, it'll prompt you to download a zipped file contianing properly rendered gem popups

urls:

- https://www.pathofexile.com/forum/view-thread/3452098
- https://www.pathofexile.com/forum/view-thread/3452250

```js
// ==UserScript==
// @name         New Userscript
// @namespace    http://tampermonkey.net/
// @version      2023-12-05
// @description  try to take over the world!
// @author       You
// @match        https://www.pathofexile.com/forum/view-thread/3452098
// @match        https://www.pathofexile.com/forum/view-thread/3452250
// @icon         https://www.google.com/s2/favicons?sz=64&domain=pathofexile.com
// @grant        none
// @require      https://html2canvas.hertzen.com/dist/html2canvas.min.js
// @require      http://code.jquery.com/jquery-3.4.1.min.js
// @require      https://cdnjs.cloudflare.com/ajax/libs/jszip/3.2.0/jszip.min.js
// ==/UserScript==

const zip = new JSZip();
function init_popups()
{
    // simulate click on gems so popups get properly initialized with proper size
    for (const gem of document.getElementsByClassName("itemContentLayout iW1 iH1 newItemContainer itemRendered"))
    {
        var evt = new MouseEvent('mouseover', {
          'view': window,
          'bubbles': true,
          'cancelable': true
        });
        gem.dispatchEvent(evt);
    }
}

async function work()
{
    for (const hover of document.getElementsByClassName("itemPopupContainer newItemPopup gemPopup"))
    {
        // position: absolute; display: none;
        // position: absolute; display: block; top: 144.359px; left: 1354.62px; width: 365.112px;
        const canvas = await html2canvas(hover, {
            allowTaint: true,
            useCORS: true,
        })
        .then(function (canvas) {
            var titleNode = hover.children[0].children[0].children[1].children[0];
            var title = titleNode.innerHTML;
            console.log(title);
            const image = canvas.toDataURL("image/png")

            canvas.toBlob(function (blob) {
                zip.file(title+".png", blob);
            });

            //canvas.remove()
            // reset style
            hover.setAttribute("style", "position: absolute; display: none;");

        })
            .catch((e) => {
        });
    }

    zip.generateAsync({type : "blob"})
        .then(zip_blob => {
        const dl = document.createElement("a");
        dl.href = URL.createObjectURL(zip_blob);
        dl.download = "gems.zip";
        dl.click();
    });
}

(function() {
    'use strict';



    $(document).ready(function() {

        $(document).keydown(function(e) {
            if (e.keyCode == 101)
            {
                init_popups();
                work();
            }
        });
    });
})();
```
