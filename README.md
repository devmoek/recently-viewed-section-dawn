🇺🇸
# How To Show Recently Viewed Items in Shopify Store using Dawn Theme?

## Create recently-viewed.liquid file
Go to `sections` folder and create `recently-viewed.liquid` file.

## Create a stylesheet file
Navigate to `assets` folder and create `section.recently.viewed.css` file, paste the code inside. By the way you can paste CSS into section file. If so, don't forget to delete first line from the section file and use liquid tags `{% style %}` *your styles here* `{% endstyle %}` to wrap your styles. More about **liquid** you can read [here](https://www.shopify.com/partners/shopify-cheat-sheet).

## Create jquery.product.min.js file
It needs to collect and store cookie data. Go to `assets` and create `jquery.product.min.js` file and paste the code into it.

## Add the section in product.json file
Don't forget about this step. It's super important! Go to `templates` folder and then click on `product.json file`, paste the code where you would like to show a recently viewed section.

🇷🇺
# Как Добавить Недавно Просмотренные в Шопифай тему Dawn?

## Создайте recently-viewed.liquid файл
Перейдите в папку `sections` и создайте `recently-viewed.liquid` файл.

## Создайте файл стилей
Перейдите в папку `assets` и создайте `section.recently.viewed.css`файл и вставьте код внутрь файла. Вы можете использовать CSS код и на странице секции. Для этого не забудьте удалить первую строчку из файла секции и используйте liquid тэги `{% style %}` *ваши стили будут тут* `{% endstyle %}` чтобы обернуть ваши стили. Чтобы почитать больше про liquid перейдите по этой [ссылке](https://www.shopify.com/partners/shopify-cheat-sheet).

## Создайте jquery.product.min.js файл
Он нужен для сбора и хранения куки продуктов. Перейдите в папку `assets` и создайте `jquery.product.min.js` файл и вставьте код.

## Добавьте секцию в product.json файл
Не забудьте об этом шаге, если вы его пропустите, то ничего не будет работать. Перейдите в папку `templates` и войдите в `product.json file`, вставьте код туда, где хотите показывать секцию недавно просмотренных товаров.
