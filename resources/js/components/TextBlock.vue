<template>
    <div>
        <h1>{{line_number}}</h1>
        <div>
            <p id="ed" v-model="html" ref="ed" class="editor" contenteditable="tr" @keyup.space="changeTester" @keyup.enter="line_number_up" @input="update"></p>
        </div>
    </div>
</template>

<script>
export default {
    name: 'TextBlock',
    data() {
        return {
            html: '',
            line_number: 0,
        }
    },
    methods: {
        update(e) {
            this.html = e.target.innerHTML;
            console.log(this.getPos());
        },
        renderHtml(e) {
            this.html += "<img src='https://cdn-images-1.medium.com/max/853/1*FH12a2fX61aHOn39pff9vA.jpeg' alt='someimage' width=200px dir='rt'>";
        },
        append(e) {
            this.html += "<p>this is a p tag</p>";
            this.updateDOM(this.html, e);
        },
        line_number_up(e) {
            let line_number_matches = this.html.match(/<br>/g);
            let line_number = line_number_matches.length;
            this.line_number = line_number;
        },
        updateDOM(new_html, e) {
            this.html = new_html;
            e.target.innerHTML = new_html;
        },
        getCaretCharacterOffsetWithin(element) {
            var caretOffset = 0;
            var doc = element.ownerDocument || element.document;
            var win = doc.defaultView || doc.parentWindow;
            var sel;
            if (typeof win.getSelection != "undefined") {
                sel = win.getSelection();
                if (sel.rangeCount > 0) {
                    var range = win.getSelection().getRangeAt(0);
                    var preCaretRange = range.cloneRange();
                    preCaretRange.selectNodeContents(element);
                    preCaretRange.setEnd(range.endContainer, range.endOffset);
                    caretOffset = preCaretRange.toString().length;
                }
            } else if ((sel = doc.selection) && sel.type != "Control") {
                var textRange = sel.createRange();
                var preCaretTextRange = doc.body.createTextRange();
                preCaretTextRange.moveToElementText(element);
                preCaretTextRange.setEndPoint("EndToEnd", textRange);
                caretOffset = preCaretTextRange.text.length;
            }
            return caretOffset;
        },
        setPos(arg) {
            var el = this.$refs.ed;
            var range = document.createRange();
            var sel = window.getSelection();
            range.setStart(el.childNodes[0], arg);
            range.collapse(true);
            sel.removeAllRanges();
            sel.addRange(range);
            el.focus();
        },
        getPos() {
            if (window.getSelection && window.getSelection().getRangeAt) {
                var range = window.getSelection().getRangeAt(0);
                var selectedObj = window.getSelection();
                var rangeCount = 0;
                var childNodes = selectedObj.anchorNode.parentNode.childNodes;
                for (var i = 0; i < childNodes.length; i++) {
                    if (childNodes[i] == selectedObj.anchorNode) {
                        break;
                    }
                    if (childNodes[i].outerHTML)
                        rangeCount += childNodes[i].outerHTML.length;
                    else if (childNodes[i].nodeType == 3) {
                        rangeCount += childNodes[i].textContent.length;
                    }
                }
                return range.startOffset + rangeCount;
            }
            return -1;
        },

        changeTester(e) {
            this.setPos(9);
        }
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