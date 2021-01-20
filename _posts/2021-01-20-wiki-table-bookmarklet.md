---
layout: post
comments: true
title: Extracting tables from Wikipedia using bookmarklet
description: Bookmarklet Wiki table extraction
summary: Bookmarklet Wiki table extraction
comments: true
tags: [bookmarklet, automation, wikipedia, side-project]
---

Continuing the theme of bookmarklets from my previous <a href="https://darensin01.github.io/2021/01/13/bto-bookmarklet" target="_blank">post</a>, I decided to create another bookmarklet to extract Wikipedia tables into CSV files. A similar tool can be found <a href="https://wikitable2csv.ggor.de/" target="_blank">here</a>.

Credits to <a href="https://stackoverflow.com/a/29304414/6621003" target="_blank">this Stack Overflow answer</a> for the code to download JavaScript arrays as CSV files. Link to this project's Github repository <a href="https://github.com/darensin01/wiki-table-to-csv" target="_blank">here</a>.

## Why this bookmarklet?
In some of my Data Science projects, I often require data from Wikipedia pages. Instead of manually copying, pasting and formatting the tables one by one, I thought it will be great to have a bookmarklet that helps to download the data that I need into a CSV file, that can be read in by libraries such as `pandas`. It's also a great way to find out how to use a bookmarklet to download content as a file.

## Features
* Automatic extraction of simple tables in Wikipedia into CSV
* Retains structure of tables
* CSV files are named according to caption of the tables. If caption is not available, they will be named after the table's enclosing header


## How to get the bookmarklet?
* Toggle your browser's bookmark bar (CTRL + Shift + B on Windows/Chrome).
* Drag the following link onto your bookmarks bar: <a href="javascript: (function() {let download = function(content, fileName, mimeType) {let a = document.createElement('a');mimeType = mimeType || 'application/octet-stream';if (navigator.msSaveBlob) { navigator.msSaveBlob(new Blob([content], { type: mimeType }), fileName); } else if (URL &amp;&amp; 'download' in a) { a.href = URL.createObjectURL(new Blob([content], { type: mimeType })); a.setAttribute('download', fileName); document.body.appendChild(a); a.click(); document.body.removeChild(a); } else { location.href = 'data:application/octet-stream,' + encodeURIComponent(content); } };let sanitise_filename = function(name) { let sanitised_name = name.toLowerCase().replace(/[\/\?&lt;&gt;\\:\*\|&quot;:]/g, '').replaceAll(' ', '_'); return sanitised_name.substring(0, 251) + '.csv'; }; let clean_text = function(t) {return t.replace(/[.,\/#!$%\^&amp;\*;:{}=\-_`~()]/g,&quot;&quot;).replace(/\s{2,}/g,&quot; &quot;);};let css_selector_table = &quot;table[class~='wikitable']&quot;;let tables = document.querySelectorAll(css_selector_table);for (let i = 0; i &lt; tables.length; i++) {let csv_content = [];let table = tables[i];if (table.style.fontSize === '90%') {continue;}let csv_filename = '';let caption = table.querySelector(&quot;caption&quot;);if (caption !== null) {csv_filename = clean_text(caption.innerText)} else {let cur_prev_sibling = table.previousElementSibling;while (cur_prev_sibling.nodeName[0] != &quot;H&quot;) {cur_prev_sibling = cur_prev_sibling.previousElementSibling;}let headline = cur_prev_sibling.querySelector(&quot;span[class='mw-headline']&quot;);csv_filename = clean_text(headline.innerText);}let headers = table.querySelectorAll('thead &gt; tr &gt; th');if (headers.length &gt; 0) {headers = Array.from(headers).map(h =&gt; h.innerText).join(',');csv_content.push(headers);}let table_rows = table.querySelectorAll('tbody &gt; tr');if (table_rows !== null) {for (let j = 0; j &lt; table_rows.length; j++) {let row = table_rows[j].querySelectorAll('th,td');let content = Array.from(row).map(h =&gt; {let text = h.innerText.replaceAll('&quot;', '\'');let text_array = text.split(&quot;\n&quot;);if (text_array.length &gt; 1) {text = text_array.join(&quot;|&quot;);}return '&quot;' + text + '&quot;';});csv_content.push(content.join(','));}} if (csv_content.length &gt; 0) { csv_content = csv_content.join(&quot;\r\n&quot;); download(csv_content, sanitise_filename(csv_filename), 'text/csv;encoding:utf-8'); } } })();" style="border-radius: 7px; background-color: #A9A9A9; padding: 2px; text-decoration: none; font-size: 150%; margin: 5px;">wiki-table</a>
* _Optional_: Rename the new bookmark as whatever you want.

## Using the bookmarklet
### Example 1
* As an example, suppose I want to extract the tables for this Wikipedia page: <a href="https://en.wikipedia.org/wiki/Singaporean_Mahjong_scoring_rules" target="_blank">Singaporean Mahjong scoring rules</a>. The first table that we see is the following:

> <img src="https://github.com/darensin01/wiki-table-to-csv/blob/main/screenshots/eg_table_1.PNG?raw=true" width="600px">

* Subsequent tables are as follow:

> <img src="https://github.com/darensin01/wiki-table-to-csv/blob/main/screenshots/eg_table_2.PNG?raw=true" width="500px">

* There are 3 tables in total. Click on the bookmarklet, and it will automatically extract the 3 tables above as 3 separate CSV files. Remember to allow your browser to download multiple files at the same time.

* These 3 tables happen to have no caption, and they fall under the same header ("Scoring points"). I decided to name the downloaded files as `scoring_points.csv`, and the downloaded files will automatically be renamed as `scoring_points.csv`, `scoring_points (1).csv` and `scoring_points (2).csv`, due to duplicate file names.

* This is how one of the CSV files look like, with 2 columns and 6 rows:

> <img src="https://github.com/darensin01/wiki-table-to-csv/blob/main/screenshots/eg_table_result_1.PNG?raw=true" width="400px">

### Example 2
* As another example, suppose I want to extract from: <a href="https://en.wikipedia.org/wiki/List_of_songs_recorded_by_Lady_Gaga" target="_blank">List of songs recorded by Lady Gaga</a>. Here's an example table:

> <img src="https://github.com/darensin01/wiki-table-to-csv/blob/main/screenshots/eg_table_3.PNG?raw=true" width="800px">

* A _caption_ exists for this table, which is enclosed in the red box above. To make the filename of the CSV file more readable and intuitive, I decided to name the file as the caption of the table. This table is saved as `name_of_song_featured_performers_writers_originating_album_and_year_released.csv`.

* Note that there is a small table above, with the caption `Key`. In this context, I regarded this small table as non-essential. I used the `fontSize` property of the caption to automatically ignore this table.


## Gotchas/To-do
For this section, see the original table from <a href="https://en.wikipedia.org/wiki/H%C3%A9roes_del_Silencio_discography" target="_blank">this Wikipedia page</a>...

> <img src="https://github.com/darensin01/wiki-table-to-csv/blob/main/screenshots/eg_table_4.png?raw=true" width="800px">

... And its resulting CSV file:

> <img src="https://github.com/darensin01/wiki-table-to-csv/blob/main/screenshots/eg_table_result_2.PNG?raw=true" width="800px">

### Multi-line content
* In the event where the content of a cell is separated by new lines, I decided to replace these spaces with a single pipe, in the event where the new lines are essential. See the result enclosed in light blue box. Downstream data processing tasks can recover the spaces again via methods such as `split`.

### Multi-row/multi-column tables
* This bookmarklet is unable to extract properly more sophisticated tables. For example, the table above splits the 3rd column into 2 separate rows, and this messes the resulting CSV file. The CSV file interprets the table as having 4 columns (see row 1 of result), and interprets the content in the multi-column as a separate row (see content enclosed in red box).

## Conclusion
This bookmarklet is convenient to extract _simple_ tables into CSV files, with some elements of automatic naming, so that one does not have to manually rename the CSV files into interpretable names. For more complex tables, more work will need to be done.

Let me know in the Disqus section below if you've used this bookmarklet, and what you think of it!
