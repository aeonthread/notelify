<template>
    <div>
        <div id="ed" v-model="html" class="editor" @keydown.tab.prevent="run" @click="getpos" contenteditable="tr" @input="update">
        </div>
    </div>
</template>

<script>
import uuid from 'uuid/v1';
export default {
    name: 'TextBlock',
    data() {
        return {
            html: '\n',
            pos: {
                start: 0,
                end: 0,
            },
            index: {
                line: [],
                current: [

                ],
            }
        }
    },
    methods: {
        run(e) {
            let html = this.indexLines();
            let newHtml = this.getFirstIfNotEmpty(html).parentElement.outerHTML;
            this.updateDOM(newHtml, e);
            // this.setPos();
        },
        getDocument() {
            let parser = new DOMParser();
            let document = parser.parseFromString(this.html, "text/html");
            return document.childNodes;
        },
        getBody() {
            let document = this.getDocument();
            let body = this.getFirstIfNotEmpty(document).childNodes[1].childNodes;
            return body;
        },
        indexLines() {
            let body = this.getBody();
            let items = this.getIfNotEmpty(body);
            items.forEach((item, i) => {
                item.id = uuid();
            });
            return body;
        },
        getIfNotEmpty(collection) {
            if (collection.length > 0) {
                return collection;
            }
            return "empty array";
        },
        getFirstIfNotEmpty(collection) {
            if (collection.length > 0) {
                return collection[0];
            }
            return "empty array";
        },
        updateDOM(new_html, e) {
            this.html = new_html;
            e.target.innerHTML = new_html;
        },
        update(e) {
            this.html = e.target.innerHTML;
        },
        getpos(e) {
            // console.log(this.getPos(e.target));
        },
        setPos(context, length) {
            var node = document.querySelector('#' + context);
            node.focus();
            var textNode = node.firstChild;
            var caret = length; // insert caret after the 10th character say
            var range = document.createRange();
            range.setStart(textNode, caret);
            range.setEnd(textNode, caret);
            var sel = window.getSelection();
            sel.removeAllRanges();
            sel.addRange(range);
        },
        getPos(element) {
            var doc = element.ownerDocument || element.document;
            var win = doc.defaultView || doc.parentWindow;
            var sel;
            if (typeof win.getSelection != "undefined") {
                sel = win.getSelection();
                if (sel.rangeCount > 0) {
                    var range = win.getSelection().getRangeAt(0);
                    var preCaretRange = range.cloneRange();
                    preCaretRange.selectNodeContents(element);
                    preCaretRange.setEnd(range.startContainer, range.startOffset);
                    this.pos.start = preCaretRange.toString().length;
                    preCaretRange.setEnd(range.endContainer, range.endOffset);
                    this.pos.end = preCaretRange.toString().length;
                }
            } else if ((sel = doc.selection) && sel.type != "Control") {
                var textRange = sel.createRange();
                var preCaretTextRange = doc.body.createTextRange();
                preCaretTextRange.moveToElementText(element);
                preCaretTextRange.setEndPoint("EndToStart", textRange);
                this.pos.start = preCaretTextRange.text.length;
                preCaretTextRange.setEndPoint("EndToEnd", textRange);
                this.pos.end = preCaretTextRange.text.length;
            }
            return { start: this.pos.start, end: this.pos.end };
        },
    },
}
</script>

<style>
span,
p,
h1,
h2,
h3,
h4,
h5 {
    display: inline;
}

.editor {
    min-height: 20px;
    white-space: pre;
    background-color: rgb(255, 247, 207);
}
</style>