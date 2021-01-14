---
layout: post
comments: true
title: Automating mouseover task for HDB BTO information with bookmarklet
description: Bookmarklet for HDB BTO information
summary: Bookmarklet for HDB BTO information
comments: true
tags: [bookmarklet, automation, side-project]
---

Recently, I created a bookmarklet to make it convenient for people to check the price and size of HDB BTO units.

The bookmarklet's GitHub repository can be found <a href="https://github.com/darensin01/hdb-flat-selection" target="_blank">here</a>.

## What is BTO?
BTO is an acronym for Build to Order. See [here](https://en.wikipedia.org/wiki/Build_to_order_(HDB)) for an explanation.

## What is a bookmarklet?
See [here](https://gist.github.com/caseywatts/c0cec1f89ccdb8b469b1) for an explanation.

## How to get the bookmarklet?
* Show/Reveal your browser's bookmark manager (CTRL + Shift + B on Windows/Chrome).
* Drag the following link onto your bookmarks bar: <a href="javascript:(function(){let css_selector_block_num = &quot;td[style~=&apos;background-color:#fffac2&apos;]&quot;;let block_num = document.querySelector(css_selector_block_num).innerText;let css_selector_units = &quot;table &gt; tbody &gt; tr &gt; td&quot;;let units = document.querySelectorAll(css_selector_units);let results = [];for (let i = 0; i &lt; units.length; i++) {    let floor_and_unit_number = units[i].innerText;    if (floor_and_unit_number[0] != &apos;#&apos;) {continue;    }let unit_number = floor_and_unit_number.split(&quot;-&quot;)[1];let css_selector_unit_tooltip = `span[data-selector=&apos;${floor_and_unit_number}&apos;]`;let tooltip_elem = document.querySelector(css_selector_unit_tooltip);if (tooltip_elem === null) {continue;    }    let tooltip_text = tooltip_elem.title;    let tooltip_array = tooltip_text.split(&quot;&lt;br&gt;&quot;);    let price_text = tooltip_array[0];    let sqm_text = tooltip_array[2];    let price = parseInt(price_text.replace(&quot;$&quot;, &quot;&quot;).replace(&quot;,&quot;, &quot;&quot;));    let sqm = parseInt(sqm_text.replace(&quot; Sqm&quot;, &quot;&quot;));    let price_per_sqm = (price / sqm).toFixed(2);    results.push({&apos;floor_and_unit_number&apos;: floor_and_unit_number,&apos;unit_number&apos;: unit_number,&apos;price&apos;: price_text,&apos;price_per_sqm&apos;: price_per_sqm,&apos;sqm&apos;: sqm_text,    });}function render(display_list) {    let unit_info_space = popup.document.getElementById(&quot;unitInformation&quot;);    unit_info_space.innerHTML = &quot;&quot;;    for (let i = 0; i &lt; display_list.length; i++) {let item = display_list[i];unit_info_space.innerHTML += `${i+1}) ${item[&apos;floor_and_unit_number&apos;]}: ${item[&apos;price&apos;]} (${item[&apos;sqm&apos;]}, $${item[&apos;price_per_sqm&apos;]}/sqm) &lt;br&gt;`;    }}let popup = window.open(&quot;&quot;, &quot;&quot;, &apos;width=400,height=400,toolbar=0,menubar=0,titlebar=0&apos;);popup.document.title = `Block ${block_num}`;popup.document.body.innerHTML = `&lt;h2&gt;Block ${block_num}&lt;/h2&gt;&lt;div id=&quot;buttons&quot; style=&quot;margin:5px;&quot;&gt;&lt;/div&gt;&lt;div id=&quot;unitInformation&quot; style=&quot;margin:5px;&quot;&gt;&lt;/div&gt;`;let unit_keys = Object.keys(results[0]);let buttons_section = popup.document.getElementById(&quot;buttons&quot;);for (let i = 0; i &lt; unit_keys.length; i++) {    let key_name = unit_keys[i];    let button = popup.document.createElement(&quot;button&quot;);    button.setAttribute(&quot;style&quot;, `display:block;margin:2px;text-decoration: none;border-radius: 5px;color: black;border: 2px solid #000000;`);    button.setAttribute(&quot;id&quot;, `sort_${key_name}_btn`);    button.onclick = function() {results.sort((a, b) =&gt; (a[key_name] &gt; b[key_name]) ? 1 : ((b[key_name] &gt; a[key_name]) ? -1 : 0));render(results);    };    let key_name_display = key_name.split(&quot;_&quot;).join(&quot; &quot;);    button.innerHTML = `Sort by ${key_name_display}`;    buttons_section.appendChild(button);}popup.document.getElementById(`sort_${Object.keys(results[0])[0]}_btn`).click();})();" style="border-radius: 7px; background-color: #A9A9A9; padding: 2px; text-decoration: none; font-size: 150%; margin: 5px;">BTO</a>
* Edit the new bookmark as whatever you want.
* Follow the steps below to use the bookmarklet.

## Steps
* From the <a href="https://services2.hdb.gov.sg/webapp/BP13AWFlatAvail/BP13SEstateSummary?sel=BTO" target="_blank">Flats Available for Selection page</a> as shown below, select a project (e.g. "4-room").

> <img src="https://github.com/darensin01/hdb-flat-selection/blob/main/screenshots/ss4.PNG?raw=true" width="600px">

* For the project selected as shown below, select a block that is available for selection (e.g. 535C).

> <img src="https://github.com/darensin01/hdb-flat-selection/blob/main/screenshots/ss5.PNG?raw=true" width="600px">

* You should see a list of flats available for selection as shown below.

> <img src="https://github.com/darensin01/hdb-flat-selection/blob/main/screenshots/ss1.PNG?raw=true" width="350px">

* Mouseover the unit of interest to reveal the size (in square meters) and its price (in Singapore Dollars), as shown below.

> <img src="https://github.com/darensin01/hdb-flat-selection/blob/main/screenshots/ss2.png?raw=true" width="350px">

* Use the bookmarklet to open a new window that displays all the mouseover information, as shown below.
* You can sort the information by price, sqm, price per sqm etc.

> <img src="https://github.com/darensin01/hdb-flat-selection/blob/main/screenshots/ss3.PNG?raw=true" width="350px">
