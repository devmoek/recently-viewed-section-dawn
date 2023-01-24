🇺🇸
# How To Show Recently Viewed Items in Shopify Store Using Dawn Theme?

## Create recently-viewed.liquid file
Go to `sections` folder and create `recently-viewed.liquid` file. Put the code below inside the file:
```
{{ 'section-recently-viewed.css' | asset_url | stylesheet_tag }}


<div class="recently-viewed-wrapper page-width">
    <h2>{{section.settings.heading}}</h2>
    <ul class="recently-viewed-grid">
    <!-- Recently viewed products will appear here -->
    </ul>
</div>

<script>
function setRecentlyViewedProducts() {
  const productData = {
    productTitle: "{{ product.title }}",
    productImg: "{{ product.featured_media | img_url: '300x' }}",
    productPrice: "{{ product.price | money }}",
    productUrl: "{{ product.url }}",
    productImageAltText: "{{product.featured_media.alt | escape }}"
  };
  const productList = [];
  let jsonResp, jsonRespArr, jsonRespArrStr;
  const numberOfProducts = 8;
  productList.push(productData);
  const currProductPageTitle = productData.productTitle;
  const productDataString = JSON.stringify(productList);
  const localData = localStorage.getItem("recentlyViewedProduct");

  if (localData === null) {
    localStorage.setItem("recentlyViewedProduct", productDataString);
  } else if (localData) {
    const oldProductData = localStorage.getItem("recentlyViewedProduct");
    const countProductData = (oldProductData.match(/productTitle/g) || []).length;
    const sameProduct = oldProductData.includes(currProductPageTitle);
    if (countProductData < numberOfProducts && sameProduct == false) {
      jsonResp = JSON.parse(oldProductData);
      jsonRespArr = jsonResp.concat(productList);
      jsonRespArrStr = JSON.stringify(jsonRespArr);
      localStorage.setItem("recentlyViewedProduct", jsonRespArrStr);
    } else if (countProductData >= numberOfProducts && sameProduct == false) {
      jsonResp = JSON.parse(oldProductData);
      jsonResp.shift();
      jsonRespArr = jsonResp.concat(productList);
      jsonRespArr = JSON.stringify(jsonRespArr);
      localStorage.setItem("recentlyViewedProduct", jsonRespArr);
    }
  }
}

setRecentlyViewedProducts();
const localViewed = localStorage.recentlyViewedProduct;
function getRecentlyViewedProducts() {
  const productData = JSON.parse(localStorage.getItem("recentlyViewedProduct"));
  const recentlyViewedHtml = [];
  productData.map(item => {
    recentlyViewedHtml.unshift(`
    <li class="recently-viewed-grid-item">
      <a href="${item.productUrl}"> 
		<img class="recently-viewed-img" src='${item.productImg}' loading="lazy" alt="${item.productImageAltText}"/>
      </a>
       <h3><a class="recently-viewed-a" href="${item.productUrl}">${item.productTitle}</a></h3>
       <p>${item.productPrice}</p>
    </li>
   `);
  });
  const newProductData = `${recentlyViewedHtml.join("")}`;
  const fullContent = document.getElementsByClassName("recently-viewed-grid");
  fullContent[0].innerHTML = newProductData;
}

document.addEventListener("DOMContentLoaded", function (event) {
  getRecentlyViewedProducts();
});

</script>

{% schema %}
  {
    "name": "Recently Products",
    "settings": [
        {
          "type": "text",
          "id": "heading",
          "default": "Recently Viewed",
          "label": "Heading"
        }
	]
  }
{% endschema %}

```

## Create a stylesheet file
Navigate to `assets` folder and create `section-recently-viewed.css` file, paste the code inside. By the way you can paste CSS into section file. If so, don't forget to delete first line from the section file and use liquid tags `{% style %}` *your styles here* `{% endstyle %}` to wrap your styles. More about **liquid** you can read [here](https://www.shopify.com/partners/shopify-cheat-sheet). 

Put the styles below inside the file:

```
.recently-viewed-wrapper {
    margin: 1rem auto;
  }

  .recently-viewed-img {
    width: 100%;
  }

  .recently-viewed-grid {
    display: grid;
    grid-gap: 1rem;
    grid-template-columns: repeat(auto-fill, minmax(min(100%, 260px), 1fr));
    list-style: none;
    margin:0; 
    padding: 0;
  }   

  .recently-viewed-grid-item {
    display: flex;
    flex-direction: column;
    text-align: center;
  }

  .recently-viewed-a {
    text-decoration: none;
    color: #000;
  }
```

## Create jquery.product.min.js file
It needs to collect and store cookie data. Go to `assets` and create `jquery.product.min.js` file and paste the code into it.

Copy and paste code inside the file:

```
/**
 * Cookie plugin
 *
 * Copyright (c) 2006 Klaus Hartl (stilbuero.de)
 * Dual licensed under the MIT and GPL licenses:
 * http://www.opensource.org/licenses/mit-license.php
 * http://www.gnu.org/licenses/gpl.html
 *
 */
 
 jQuery.cookie=function(b,j,m){if(typeof j!="undefined"){m=m||{};if(j===null){j="";m.expires=-1}var e="";if(m.expires&&(typeof m.expires=="number"||m.expires.toUTCString)){var f;if(typeof m.expires=="number"){f=new Date();f.setTime(f.getTime()+(m.expires*24*60*60*1000))}else{f=m.expires}e="; expires="+f.toUTCString()}var l=m.path?"; path="+(m.path):"";var g=m.domain?"; domain="+(m.domain):"";var a=m.secure?"; secure":"";document.cookie=[b,"=",encodeURIComponent(j),e,l,g,a].join("")}else{var d=null;if(document.cookie&&document.cookie!=""){var k=document.cookie.split(";");for(var h=0;h<k.length;h++){var c=jQuery.trim(k[h]);if(c.substring(0,b.length+1)==(b+"=")){d=decodeURIComponent(c.substring(b.length+1));break}}}return d}};

 /**
  * Module to show Recently Viewed Products
  *
  * Copyright (c) 2014 Caroline Schnapp (11heavens.com)
  * Dual licensed under the MIT and GPL licenses:
  * http://www.opensource.org/licenses/mit-license.php
  * http://www.gnu.org/licenses/gpl.html
  *
  */
  
 Shopify.Products=(function(){var a={howManyToShow:3,howManyToStoreInMemory:10,wrapperId:"recently-viewed-products",templateId:"recently-viewed-product-template",onComplete:null};var c=[];var h=null;var d=null;var e=0;var b={configuration:{expires:90,path:"/",domain:window.location.hostname},name:"shopify_recently_viewed",write:function(i){jQuery.cookie(this.name,i.join(" "),this.configuration)},read:function(){var i=[];var j=jQuery.cookie(this.name);if(j!==null){i=j.split(" ")}return i},destroy:function(){jQuery.cookie(this.name,null,this.configuration)},remove:function(k){var j=this.read();var i=jQuery.inArray(k,j);if(i!==-1){j.splice(i,1);this.write(j)}}};var f=function(){h.show();if(a.onComplete){try{a.onComplete()}catch(i){}}};var g=function(){if(c.length&&e<a.howManyToShow){jQuery.ajax({dataType:"json",url:"/products/"+c[0]+".js",cache:false,success:function(i){d.tmpl(i).appendTo(h);c.shift();e++;g()},error:function(){b.remove(c[0]);c.shift();g()}})}else{f()}};return{resizeImage:function(m,j){if(j==null){return m}if(j=="master"){return m.replace(/http(s)?:/,"")}var i=m.match(/\.(jpg|jpeg|gif|png|bmp|bitmap|tiff|tif)(\?v=\d+)?/i);if(i!=null){var k=m.split(i[0]);var l=i[0];return(k[0]+"_"+j+l).replace(/http(s)?:/,"")}else{return null}},showRecentlyViewed:function(i){var i=i||{};jQuery.extend(a,i);c=b.read();d=jQuery("#"+a.templateId);h=jQuery("#"+a.wrapperId);a.howManyToShow=Math.min(c.length,a.howManyToShow);if(a.howManyToShow&&d.length&&h.length){g()}},getConfig:function(){return a},clearList:function(){b.destroy()},recordRecentlyViewed:function(l){var l=l||{};jQuery.extend(a,l);var j=b.read();if(window.location.pathname.indexOf("/products/")!==-1){var k=window.location.pathname.match(/\/products\/([a-z0-9\-]+)/)[1];var i=jQuery.inArray(k,j);if(i===-1){j.unshift(k);j=j.splice(0,a.howManyToStoreInMemory)}else{j.splice(i,1);j.unshift(k)}b.write(j)}}}})();

```

## Add the section in product.json file
Don't forget about this step. It's super important! Go to `templates` folder and then click on `product.json file`, paste the code where you would like to show a recently viewed section.

```
    "recently-viewed": {
        "type": "recently-viewed",
        "settings": {
        }
      }
```

Don't forget to indicate this section in order, like so:
```
    "order": [
      "main",
      "recently-viewed"
    ]
```

You can see a little bit different product page layout. It's okay. Hope it helps :)

🇷🇺
# Как Добавить Секцию Недавно Просмотренных Товаров в Шопифай тему Dawn?

## Создайте recently-viewed.liquid файл
Перейдите в папку `sections` и создайте `recently-viewed.liquid` файл.

## Создайте файл стилей
Перейдите в папку `assets` и создайте `section-recently-viewed.css`файл и вставьте код внутрь файла. Вы можете использовать CSS код и на странице секции. Для этого не забудьте удалить первую строчку из файла секции и используйте liquid тэги `{% style %}` *ваши стили будут тут* `{% endstyle %}` чтобы обернуть ваши стили. Чтобы почитать больше про liquid перейдите по этой [ссылке](https://www.shopify.com/partners/shopify-cheat-sheet).

## Создайте jquery.product.min.js файл
Он нужен для сбора и хранения куки продуктов. Перейдите в папку `assets` и создайте `jquery.product.min.js` файл и вставьте код.

## Добавьте секцию в product.json файл
Не забудьте об этом шаге, если вы его пропустите, то ничего не будет работать. Перейдите в папку `templates` и войдите в `product.json file`, вставьте код туда, где хотите показывать секцию недавно просмотренных товаров.
