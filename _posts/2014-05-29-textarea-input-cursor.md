---
layout: post
title: textarea光标位置定位、插入、获取选择文字
description: textarea光标位置定位、获取选择文字、向光标位置插入指定字符串
keywords: textarea,document.selection,selectionStart,selectionEnd
categories: javascript
---

微博"@"功能相信每一个玩过微博的人都用过，当你输入"@"字符串时，在光标位置旁边就会生成一个提示框，你可以快速的选取你最近"@"过的人。

不过想要操作textarea可是一件麻烦事。由于获取选中区域的接口属于BOM，不同浏览器下的接口差别很大。

先来看看IE下的接口 <span class="impo">document.selection</span>

### document.selection ###
#### &nbsp;&nbsp;method ####
><span class="impo">clear</span> : Clears the contents of the selection.

><span class="impo">createRange</span> : Creates a TextRange object from the current text selection, or a controlRange collection from a control selection.

><span class="impo">createRangeCollection</span> : Creates a TextRange object collection from the current selection.

><span class="impo">empty</span> : Cancels the current selection, sets the selection type to none, and sets the item property to null.

#### &nbsp;&nbsp;properties ####
><span class="impo">type</span> : Retrieves the type of selection.

><span class="impo">typeDetail</span> : Retrieves the name of the selection type.

值得注意的是，从IE11开始不再支持<span class="impo">selection</span>对象.请使用<span class="impo">getSelection</span>对象.


再来看看FF提供的<span class="impo">document.getSelection</span>

### document.getSelection ###
#### &nbsp;&nbsp;method ####
><span class="impo">getRangeAt</span> : Returns a range object representing one of the ranges currently selected.

>注意,用户用鼠标在同一页面永远都只有一个选择区域,所以一般情况getRangeAt只能接收<span class="impo">0</span>这一个参数.但用脚本可以创建多个选择区域(Chrome下通过脚本也只能创建一个选择区域)

><span class="impo">collapse(parentNode,offset)</span> : Collapses the current selection to a single point.

>压缩选择区域至单个光标(在输入框中)位置

>&nbsp;&nbsp;<span class="para">parentNode</span>:光标将要移动到的目标元素

>&nbsp;&nbsp;<span class="para">offset</span>:光标在目标元素中所在偏移量.这里的偏移量会有两个值"0"和"1",分别表示parentNode文本的起始位置和结束位置.

><span class="impo">extend(parentNode,offset)</span>:Moves the focus of the selection to a specified point.

>&nbsp;&nbsp;<span class="para">parentNode</span>:光标将要移动到的目标元素

>&nbsp;&nbsp;<span class="para">offset</span>:光标在目标元素中所在偏移量.这里的偏移量会有两种情况:

>&nbsp;&nbsp;&nbsp;&nbsp;1.目标元素只有textNode.这时<span class="para">offset</span>的值表示光标在字符串中的位置

>&nbsp;&nbsp;&nbsp;&nbsp;2.目标元素含有非textNode子元素.这时<span class="para">offset</span>的值表示光标定位到第<span class="para">offset</span>个元素的末尾.

><span class="impo">modify(alert,direction,granularity)</span> : Changes the current selection.

>改变选择区域

>&nbsp;&nbsp;<span class="para">alert</span>:声明修改类型(move || extend)

>&nbsp;&nbsp;<span class="para">direction</span>:移动(合并)的方向.可能的值<span class="para">forward</span>,<span class="para">backward</span>,<span class="para">left</span>,<span class="para">right</span>.

>&nbsp;&nbsp;<span class="para">granularity</span>:移动(合并)的单位.可能的值<span class="para">charracter</span>,<span class="para">word</span>,<span class="para">sentence</span>,<span class="para">line</span>,<span class="para">paragraph</span>,<span class="para">lineboundary</span>,<span class="para">sentenceboundary</span>,<span class="para">paragraphboundary</span>,<span class="para">documentboundary</span>.(注意,FF不支持<span class="para">sentence</span>,<span class="para">paragraph</span>,<span class="para">sentenceboundary</span>,<span class="para">paragraphboundary</span>,<span class="para">documentboundary</span>.值得一提的是在被选择内容为中文时,FF不支持"granularity=<span class='para'>word</span>",但在Chrome上能正常使用.)

><span class="impo">collapseToStart</span> : Collapses the selection to the start of the first range in the selection.

>将选择区域压缩至区域开始的位置

><span class="impo">collapseToEnd</span> : Collapses the selection to the end of the last range in the selection.

>将选择区域压缩至区域结束的位置

><span class="impo">selectAllChildren(parentNode)</span> : Adds all the children of the specified node to the selection.

>选择<span class="para">parentNode</span>的所有子节点内容

><span class="impo">addRange(range)</span> : A Range object that will be added to the selection.

>将指定的区域添加到已选择区域

>&nbsp;&nbsp;<span class="para">range</span>:指定区域<span class="para">range</span>可以通过<span class="impo">document.createRange</span>创建,也可以通过<span class="impo">getRangeAt</span>从已有区域选择

><span class="impo">removeRange(range)</span> : Removes a range from the selection.

>从选择区域中删除指定的区域

><span class="impo">removeAllRanges</span> : Removes all ranges from the selection.

>删除所有选择区域

><span class="impo">deleteFromDocument</span> : Deletes the selection's content from the document.

>删除选择区域中的文字(只能删除文字,不会删除DOM元素)

><span class="impo">selectionLanguageChangae</span> : Modifies the cursor Bidi level after a change in keyboard direction.

>在写这篇文章时,这个方法貌似已经不存在了,但官方文档还保留了这个方法.

><span class="impo">toString</span> : Returns a string currently being represented by the selection object, i.e. the currently selected text.

>输出当前选择区域文本

><span class="impo">containNode(aNode,aPartlyContained)</span> : Indicates if a certain node is part of the selection.

>检测某些节点,当它属于选择区域时返回true,否则返回false

>&nbsp;&nbsp;<span class="para">aNode</span>:待检测的节点

>&nbsp;&nbsp;<span class="para">aPartlyContained</span>:检测类型

>&nbsp;&nbsp;&nbsp;&nbsp;1.<span class="para">aPartlyContained</span>=true:当<span class="para">aNode</span>的部分或全部属于选择区域的一部分时,则返回true

>&nbsp;&nbsp;&nbsp;&nbsp;1.<span class="para">aPartlyContained</span>=false:当<span class="para">aNode</span>整个节点属于选择区域的一部分时,则返回true(<span class="para">aNode</span>等于选择区域时也返回false,也就是说<span class="para">aNode</span>必须小于选择区域才会返回true)

#### &nbsp;&nbsp;properties ####
><span class="impo">anchorNode</span> : Returns the Node in which the selection begins.

>返回选择区域开始位置所在的节点

><span class="impo">anchorOffset</span> : Returns a number representing the offset of the selection's anchor within the anchorNode. If anchorNode is a text node, this is the number of characters within anchorNode preceding the anchor. If anchorNode is an element, this is the number of child nodes of the anchorNode preceding the anchor.

>返回选择区域开始的位置.如果选择区域是纯文本,返回选择区域中第一个字符的位置.如果选择区域是元素,返回那些在选择区域前面的同时也属于<span class="para">anchorNode</span>的子节点的个数.

><span class="impo">focusNode</span> : Returns the Node in which the selection ends.

>返回选择区域结束位置所在的节点

><span class="impo">focusOffset</span> : Returns a number representing the offset of the selection's anchor within the focusNode. If focusNode is a text node, this is the number of characters within focusNode preceding the focus. If focusNode is an element, this is the number of child nodes of the focusNode preceding the focus.

>参照<span class="impo">anchorOffset</span>

><span class="impo">ifCollapsed</span> : Returns a Boolean indicating whether the selection's start and end points are at the same position.

>判断选择区域开始于结束位置是否相同

><span class="impo">rangeCount</span> : Returns the number of ranges in the selection.

>返回<span class="impo">getSelection</span>中的<span class="impo">range</span>数

<textarea id="test" style="width:700px;height:200px;padding:4px 5px;margin:10px auto;resize:none;">hellow world</textarea>
<button id="insertStr">点我插入"\_(:3」∠)\_"内容</button> <button id="getStr">点我获取选择内容</button> <button id="showOffset">点我获取光标当前位置</button>

<script type="text/javascript">
(function(){
var $ = function(s){return document.getElementById(s)},
	text = $('test'),
	insert = $('insertStr'),
	get = $('getStr'),
	show = $('showOffset');

insert.onclick=function(){insertString(text,'_(:3」∠)_');}
get.onclick=function(){showSelection(text);}
show.onclick=function(){alert(getAnchor(text));}

function insertString(ele, str){
    var r = null,newstart = 0,tb = ele.nodeType == 1 ? ele : docuemnt.body;
    tb.focus();
    if (document.all){
        r = document.selection.createRange();
        document.selection.empty();
        r.text = str;
        r.collapse();
        r.select();
    }else{
        newstart = tb.selectionStart+str.length;
        tb.value=tb.value.substr(0,tb.selectionStart)+str+tb.value.substring(tb.selectionEnd);
        tb.selectionStart = newstart;
        tb.selectionEnd = newstart;
    }
}
function getSelection(ele){
    var sel = '',r = null,tb = ele.nodeType == 1 ? ele : docuemnt.body;
    if (document.all){
        r = document.selection.createRange();
        document.selection.empty();
        sel = r.text;
    }else{
        sel = tb.value.substring(tb.selectionStart, tb.selectionEnd);
    }
    return sel;
}
function showSelection(ele){
    var sel = getSelection(ele);
    alert('选中的文本是：'+sel);
}
function getAnchor(ele){
	var index = 0,r = null,tb = ele.nodeType == 1 ? ele : document.body;
	if(document.all){
		r = document.selection.createRange();
		tb.focus();
		r.moveStart('character', -tb.value.length); 
		index = r.text.length;	
	}else{
		index = tb.selectionStart
	}
	return index
}
})();
</script>

{% highlight javascript  %}
/*向输入框当前位置插入字符*/
function InsertString(ele, str){
    var r = null,newstart = 0,tb = ele.nodeType == 1 ? ele : docuemnt.body;
    tb.focus();
    if (document.all){
        r = document.selection.createRange();
        document.selection.empty();
        r.text = str;
        r.collapse();
        r.select();
    }else{
        newstart = tb.selectionStart+str.length;
        tb.value=tb.value.substr(0,tb.selectionStart)+str+tb.value.substring(tb.selectionEnd);
        tb.selectionStart = newstart;
        tb.selectionEnd = newstart;
    }
}
/*获取当前选择区域文字*/
function GetSelection(ele){
    var sel = '',r = null,tb = ele.nodeType == 1 ? ele : docuemnt.body;
    if (document.all){
        r = document.selection.createRange();
        document.selection.empty();
        sel = r.text;
    }else{
        sel = tb.value.substring(tb.selectionStart, tb.selectionEnd);
    }
    return sel;
}
function ShowSelection(ele){
    var sel = GetSelection(ele);
    alert('选中的文本是：'+sel);
}
/*获取光标位置*/
function getAnchor(ele){
	var index = 0,r = null,tb = ele.nodeType == 1 ? ele : document.body;
	if(document.all){
		r = document.selection.createRange();
		tb.focus();
		r.moveStart('character', -tb.value.length); 
		index = r.text.length;	
	}else{
		index = tb.selectionStart
	}
	return index
}
{% endhighlight %}

[selection的官方文档](http://msdn.microsoft.com/zh-cn/library/ie/ms535869\(v=vs.85\).aspx)

[getSelection的官方文档](https://developer.mozilla.org/en-US/docs/Web/API/Selection)
