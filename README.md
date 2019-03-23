# SuperObject

### What is JSON ?

JSON (JavaScript Object Notation) is a lightweight data-interchange format.  
It is easy for humans to read and write.  
It is easy for machines to parse and generate.  
It is based on a subset of the JavaScript Programming Language, Standard ECMA-262 3rd Edition - December 1999\.  
JSON is a text format that is completely language independent but uses conventions that are familiar to programmers.  
These properties make JSON an ideal data-interchange language.  
You can get more informations on [json.org](http://www.json.org).

<pre id="exCode">{
  "name": <span id="lit">"Henri Gourvest"</span>, <span id="cmt">/* this is a comment */</span>
  "vip": true,
  "telephones": [<span id="lit">"000000000"</span>, <span id="lit">"111111111111"</span>],
  "age": 33,
  "size": 1.83,
  "adresses": [
    {
      "adress": <span id="lit">"blabla"</span>,
      "city": <span id="lit">"Metz"</span>,
      "pc": 57000
    },
    {
      "adress": <span id="lit">"blabla"</span>,
      "city": <span id="lit">"Nantes"</span>,
      "pc": 44000
    }
  ]
}
</pre>

### Parsing a JSON data structure

<pre id="exCode"><span id="keyw">var</span>
  obj: ISuperObject;
<span id="keyw">begin</span>
  obj := SO(<span id="lit">'{"foo": true}'</span>);
  obj := TSuperObject.ParseString(<span id="lit">'{"foo": true}'</span>);
  obj := TSuperObject.ParseStream(stream);
  obj := TSuperObject.ParseFile(FileName);
<span id="keyw">end;</span>
</pre>

### Accessing data

There isn't individual datastructure for each supported data types.  
They are all an object: the **ISuperObject**.

<pre id="exCode">  val := obj.AsString;
  val := obj.AsInteger;
  val := obj.AsBoolean;
  val := obj.AsDouble;
  val := obj.AsArray;
  val := obj.AsObject;
  val := obj.AsMethod;
</pre>

#### How to read a property value of an object ?

<pre id="exCode">  val := obj.AsObject.S[<span id="lit">'foo'</span>]; <span id="cmt">// get a string</span>
  val := obj.AsObject.I[<span id="lit">'foo'</span>]; <span id="cmt">// get an Int64</span>
  val := obj.AsObject.B[<span id="lit">'foo'</span>]; <span id="cmt">// get a Boolean</span>
  val := obj.AsObject.D[<span id="lit">'foo'</span>]; <span id="cmt">// get a Double</span>
  val := obj.AsObject.O[<span id="lit">'foo'</span>]; <span id="cmt">// get an Object (default)</span>
  val := obj.AsObject.M[<span id="lit">'foo'</span>]; <span id="cmt">// get a Method</span>
  val := obj.AsObject.N[<span id="lit">'foo'</span>]; <span id="cmt">// get a null object</span>
</pre>

#### How to read a value from an array ?

<pre id="exCode">  <span id="cmt">// the advanced way</span>
  val := obj.AsArray.S[0]; <span id="cmt">// get a string</span>
  val := obj.AsArray.I[0]; <span id="cmt">// get an Int64</span>
  val := obj.AsArray.B[0]; <span id="cmt">// get a Boolean</span>
  val := obj.AsArray.D[0]; <span id="cmt">// get a Double</span>
  val := obj.AsArray.O[0]; <span id="cmt">// get an Object (default)</span>
  val := obj.AsArray.M[0]; <span id="cmt">// get a Method</span>
  val := obj.AsArray.N[0]; <span id="cmt">// get a null object</span>
</pre>

#### Using paths

Using paths is a **very productive** method to find an object when you know where is it.  
This is some usage cases:

<pre id="exCode">  obj[<span id="lit">'foo'</span>]; <span id="cmt">// get a property</span>
  obj[<span id="lit">'123'</span>]; <span id="cmt">// get an item array</span>
  obj[<span id="lit">'foo.list'</span>]; <span id="cmt">// get a property from an object</span>
  obj[<span id="lit">'foo[123]'</span>]; <span id="cmt">// get an item array from an object</span>
  obj[<span id="lit">'foo(1,2,3)'</span>]; <span id="cmt">// call a method</span>
  obj[<span id="lit">'foo[]'</span>] := value; <span id="cmt">// add an item array</span>
</pre>

you also can encapsulate paths:

<pre id="exCode">  obj := so(<span id="lit">'{"index": 1, "items": ["item 1", "item 2", "item 3"]}'</span>);
  obj[<span id="lit">'items[index]'</span>] <span id="cmt">// return "item 2"</span>
</pre>

or recreate a new data structure from another:

<pre id="exCode">  obj := so(<span id="lit">'{"index": 1, "items": ["item 1", "item 2", "item 3"]}'</span>);
  obj[<span id="lit">'{"item": items[index], "index": index}'</span>] <span id="cmt">// return {"item": "item 2", "index": 1}</span>
</pre>

### Browsing data structure

#### Using Delphi enumerator.

Using Delphi enumerator you can browse item's array or property's object value in the same maner.

<pre id="exCode"><span id="keyw">var</span>
  item: ISuperObject;
<span id="keyw">begin</span>
  <span id="keyw">for</span> item <span id="keyw">in</span> obj[<span id="lit">'items'</span>] <span id="keyw">do</span> ...
</pre>

you can also browse the keys and values of an object like this:

<pre id="exCode"><span id="keyw">var</span>
  item: TSuperAvlEntry;
<span id="keyw">begin</span>
  <span id="keyw">for</span> item <span id="keyw">in</span> obj.AsObject <span id="keyw">do</span> ...
  <span id="keyw">begin</span>
    item.Name;
    item.Value;
  <span id="keyw">end</span>;
</pre>

#### Browsing object properties without enumerator

<pre id="exCode"><span id="keyw">var</span>
  item: TSuperObjectIter;
<span id="keyw">begin</span>
  <span id="keyw">if</span> ObjectFindFirst(obj, item) <span id="keyw">then</span>
  <span id="keyw">repeat</span>
    item.key;
    item.val;
  <span id="keyw">until</span> <span id="keyw">not</span> ObjectFindNext(item);
  ObjectFindClose(item);
</pre>

#### Browsing array items without enumerator

<pre id="exCode"><span id="keyw">var</span>
  item: Integer;
<span id="keyw">begin</span>
  <span id="keyw">for</span> item := 0 <span id="keyw">to</span> obj.AsArray.Length - 1 <span id="keyw">do</span>
    obj.AsArray[item]
</pre>

### RTTI & marshalling in Delphi 2010

<pre id="exCode"><span id="keyw">type</span>
  TData = <span id="keyw">record</span>
    str: string;
    int: Integer;
    bool: Boolean;
    flt: Double;
  <span id="keyw">end;</span>
<span id="keyw">var</span>
  ctx: TSuperRttiContext;
  data: TData;
  obj: ISuperObject;
<span id="keyw">begin</span>
  ctx := TSuperRttiContext.Create;
  <span id="keyw">try</span>
    data := ctx.AsType<TData>(SO(<span id="lit">'{str: "foo", int: 123, bool: true, flt: 1.23}'</span>));
    obj := ctx.AsJson<TData>(data);
  <span id="keyw">finally</span>
    ctx.Free;
  <span id="keyw">end;</span>
<span id="keyw">end;</span>
</pre>

### Saving data

<pre id="exCode">  obj.AsJSon(options);
  obj.SaveTo(stream);
  obj.SaveTo(filename);
</pre>

### Helpers

<pre id="exCode">  SO([<span id="lit">'prop1'</span>, true, <span id="lit">'prop2'</span>, 123]); <span id="cmt">// return an object {"prop1": true, "prop2": 123}</span>
  SA([true, 123]); <span id="cmt">// return an array [true, 123]</span>
</pre>

### Non canonical forms

The SuperObject is able to parse non canonical forms.

<pre id="exCode"><span id="cmt">// unquoted identifiers</span>
SO(<span id="lit">'{foo: true}'</span>);
<span id="cmt">// unescaped or unquoted strings</span>
SO(<span id="lit">'{собственность: bla bla bla}'</span>);
<span id="cmt">// excadecimal</span>
SO(<span id="lit">'{foo: \xFF}'</span>);
</pre>
