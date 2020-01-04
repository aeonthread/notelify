<template>
    <div>
        <div id="ed" v-model="html" ref="ed" class="editor" @keydown.tab="change" @click="getpos" contenteditable="tr" @input="update"></div>
    </div>
</template>

<script>
export default {
    name: 'TextBlock',
    data() {
        return {
            html: '',
            pos: {
                start: 0,
                end: 0,
            },
        }
    },
    methods: {
        update(e) {
            this.html = e.target.innerHTML;
            // this.getpos(e);
            // this.change(e);
        },
        getpos(e) {
            console.log(this.getPos(e.target));
        },
        renderHtml(e) {
            this.html += "<img src='https://cdn-images-1.medium.com/max/853/1*FH12a2fX61aHOn39pff9vA.jpeg' alt='someimage' width=200px dir='rt'>";
        },
        updateDOM(new_html, e) {
            this.html = new_html;
            e.target.innerHTML = new_html;
        },
        change(e) {
            e.preventDefault();
            let source = this.html.split(' ');
            let word;
            let command;
            var indexOfB = source.findIndex(function(item) {
                return item.includes('`');
            });
            let temp = source.filter((item) => {
                if (indexOfB) {
                    let index = item.indexOf('`');
                    word = item.substr(0, index);
                    command = item.substr(index + 1, item.length);
                }
                return item;
            });
            source[indexOfB] = "<b id='b'>" + word + "</b>";
            let new_html = source.join(' ');
            this.updateDOM(new_html, e);
            this.setPos('b');
        },
        setPos(context) {
            var node = document.querySelector(context);
            node.focus();
            var textNode = node.firstChild;
            var caret = 4; // insert caret after the 10th character say
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
.editor {
    min-height: 20px;
    white-space: pre;
    background-color: rgb(255, 247, 207);
}
</style>