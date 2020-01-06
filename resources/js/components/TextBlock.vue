<template>
    <div>
        <div id="ed" v-model="html" class="editor" @keydown.tab.prevent="run" @click="getpos" contenteditable="tr" @input="update">
        </div>
    </div>
</template>

<script>
import uuidv1 from 'uuid/v1';
export default {
    name: 'TextBlock',
    data() {
        return {
            html: '',
            pos: {
                start: 0,
                end: 0,
            },
            index: [],
        }
    },
    methods: {
        store(i, w, t, l) {
            this.index.push({
                id: i,
                line: l,
                word: w,
                tag: t,
                pos: this.pos.start
            });
        },
        exists(word, line) {
            let index = this.index;
            let source = index.map(item => {
                if (item.pos == this.pos.start && item.line == line) {
                    return item;
                }
            });
            return source;
        },
        run(e) {
            let source = this.html.split('\n');
            let temp = [];
            let word;
            let index
            let command;
            let template;
            let id;
            source.map((item, i) => {
                if (item) {
                    index = item.indexOf('`');
                    word = item.trim();
                    command = "p";
                    if (index !== -1) {
                        word = item.substr(0, index);
                        command = item.substr(index + 1, item.length);
                    }
                    let find = this.exists(word, i);
                    console.log(find);
                    if (find.length === 0 | find[0] === undefined) {
                        let uuid = uuidv1();
                        this.store(uuid, word, command, i + 1);
                        template = '<' + command + ">" + word + '</' + command + '>';
                    } else {
                        this.index.map(item => {
                            if (item.id === find[0].id) {
                                item.tag = command;
                            }
                            template = '<' + item.tag + ">" + word + '</' + item.tag + '>';
                        });
                    }
                    temp.push(template);
                }
            });
            let new_html = temp.join('\n');
            this.updateDOM(new_html, e);
            this.setPos(e.target.id + ' ' + command, word.length);
        },
        update(e) {
            this.html = e.target.innerText;
            this.getpos(e);
        },
        getpos(e) {
            console.log(this.getPos(e.target));
        },
        updateDOM(new_html, e) {
            this.html = new_html;
            e.target.innerHTML = new_html;
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