Ripped from https://gist.github.com/cowboy/742952

From the jQuery API docs for .load():

jQuery uses the browser's .innerHTML property to parse the retrieved
document and insert it into the current document. During this process,
browsers often filter elements from the document such as <html>,
<title>, or <head> elements. As a result, the elements retrieved by
.load() may not be exactly the same as if the document were retrieved
directly by the browser.

Using jQuery, and given this test.html:

<!DOCTYPE HTML>
<html lang="en-US">
<head>
  <title>Test page</title>
</head>
<body>
  <div id="content">
    <p>stuff</p>
    <p>more stuff</p>
  </div>
</body>
</html>

This behavior can be seen:

$.get( 'test.html', function( html ) {
  // Not great: [, <title>​Test page​</title>, , <div id=​"content">​…​</div>, ]
  console.log( $(html) );
  
  // This fails: []
  console.log( $(html).find( '#content') );
  
  // This selects the content div, but.. ugly.
  console.log( $(html).filter( '#content') );
  
  // This also selects the content div, but.. also ugly.
  console.log( $('<div/>').html( html ).find( '#content' ) );
});

This, on the other hand, works as you'd expect, and attributes should
be properly preserved:

$.get( 'test.html', function( html ) {
  var hd = $.htmlDoc( html );
  
  console.log( hd.filter( 'html' ).length ); // 1
  console.log( hd.filter( 'html' ).attr( 'lang' ) ); // "en-US"
  console.log( hd.find( 'head' ).length ); // 1
  console.log( hd.find( 'body' ).length ); // 1
});
