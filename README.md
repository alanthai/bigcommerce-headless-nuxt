# bc-nuxt-vue-starter

https://github.com/bigcommerce/bc-nuxt-vue-starter

> BigCommerce + Nuxt + Storefront UI Starter

## UI Features

> 1. Product [Click here for guide](https://www.youtube.com/watch?v=B1mtNPgKHew)
>    > - Get products by category
>    > - Search products in search bar
>    > - Get product with details by slug
>    >   - Add to cart
>    >   - Add to wishlist
> 2. Cart [Click here for guide](https://www.youtube.com/watch?v=pU0JH98ifmc)
>    > - Get cart
>    > - Create cart
>    > - Add item to cart
>    > - Delete cart
> 3. Checkout ("redirected", "embedded", "custom")
>    > - "redirected" - redirect to default checkout [Click here for guide](https://www.youtube.com/watch?v=FZi_7kn7W28)
>    > - "embedded" - embed a checkout module in checkout page [Click here for guide](https://www.youtube.com/watch?v=B9W80JSYrXU)
>    > - "custom" - customize checkout page [Click here for guide](https://www.youtube.com/watch?v=jzAGDeLeYts)
>    >   - Get checkout
>    >   - Add shipping address to checkout
>    >   - Add billing address to checkout
>    >   - Create an order based on checkout
>    >   - Confirm and pay the order
> 4. Profile
>    > - Personal details (This is customer details) [Click here for guide](https://www.youtube.com/watch?v=0z3Vf5ZAUg0)
>    >   - Login
>    >   - Register
>    > - Shipping details (This is customer shipping address) [Click here for guide](https://www.youtube.com/watch?v=IRgXry6ctZk)
>    >   - Add new shipping address
>    >   - Update shipping address
>    >   - Delete shipping address
>    > - Order history (This is customer order history)
>    > - Wishlist (This is wishlist of products) [Click here for guide](https://www.youtube.com/watch?v=J2N6I_SxQiM)
>    >   - List wishlist items
>    >   - Remove item in wishlist

## Build Setup

```
# install dependencies
$ npm install

# ENV
- Create .env file from .env.example.
- Input your Store URL and Storefront API Token

# serve with hot reload at localhost:3000
$ npm run dev

# build for production and launch server
$ npm run build
$ npm run start

# generate static project
$ npm run generate
```

## Deploy non-static project on heroku

```
Firstly, please create free heroku account.

1. Please follow up below commands.
   brew install heroku/brew/heroku
   heroku login
   heroku create {Heroku App Name} - i.e: heroku create bc-vue-nuxt-starter
   npm run set-heroku-env
2. Please make you are on the latest updates of your current branch codebase.
3. After checking of these, please change something.

- Change "target: 'static'" to "target: 'server'" in nuxt.config.js
- Remove .env from .gitignore.

4. Run commands
   git add .
   git commit -am "commit message"
   git push heroku master
```

### For detailed explanation on how things work, check out [Nuxt.js docs](https://nuxtjs.org).

## Requirements

You will need a channel.

```json
POST https://api.bigcommerce.com/stores/{{store_hash}}/v3/channels
{
  "is_listable_from_ui": false,
  "is_visible": true,
  "name": {channel_name},
  "status": "active",
  "type": "storefront",
  "platform": "custom",
  "config_meta": {
    "app": {
      "id": {app id},
      "sections": [{ "title": {title}, "query_path": {query path} }]
    }
  }
}

Or you can just run a CLI.
$ npm run create-storefront-channel --name {channel name} --appId {app id} --sections {sections}
i.e -> npm run create-storefront-channel --name "channel name" --appId 4949 --sections '[{"title":"Overview", "query_path":"overview"}]'
Data type should be matched like above
```

You'll need a BigCommerce store and a token for our GraphQL Storefront API, which you can create in the API Account section of the control panel. Once you have your BC API credentials, here's a sample request to create a token:

```json
POST https://api.bigcommerce.com/stores/{{store_hash}}/v3/storefront/api-token
{
  "channel_id": 1,
  "expires_at": 2133443661,
  "allowed_cors_origins": ["http://localhost:3000"]
}

Or you can just run a CLI.
$ npm run create-storefront-token --channelId {channel id} --hostUrl {host url}
channel_id - you will get this id from above command.
i.e -> npm run create-storefront-token --channelId 123455 --hostUrl http://localhost:3000

(*) If you need to deploy on vercel, you should set the origin as the initially deployed URL and create this token.
```

You will need a site.

```json
POST https://api.bigcommerce.com/stores/{{store_hash}}/v3/channels/{{channelId}}/site
{
  "channel_id": 123456,
  "url": "http://store.example.com"
}

Or you can just run a CLI.
$ npm run create-storefront-site --channelId {channel id} --siteUrl {site url}
i.e -> npm run create-storefront-site --channelId 123456 --siteUrl "http://store.example.com"
```

You will need a route.

```json
POST https://api.bigcommerce.com/stores/{{store_hash}}/v3/sites/{{siteId}}/routes
{
  "type": "product",
  "route": "/product/book",
  "matching": "*"
}

Or you can just run a CLI.
$ npm run create-storefront-route --siteId {site id} --type {type} --route {route}
i.e -> npm run create-storefront-route --siteId 1001 --type "product" --route "/product/book"
If you don't input these params, default routes will be added.
[
  {
    "type": "cart",
    "matching": "*",
    "route": "/cart"
  },
  {
    "type": "product",
    "matching": "*",
    "route": "/products/{slug}"
  },
  {
    "type": "category",
    "matching": "*",
    "route": "/{slug}"
  },
  {
    "type": "home",
    "matching": "*",
    "route": "/"
  },
  {
    "type": "account_order_status",
    "matching": "*",
    "route": "/login?action=view_order_status"
  },
  {
    "type": "create_account",
    "matching": "*",
    "route": "/register"
  },
  {
    "type": "login",
    "matching": "*",
    "route": "/login"
  }
]
```

## How to use sample translation JSON file

```
{
  "es": [
    {
      "[Sample] 1 L Le Parfait Jar": "[Muestra] Tarro Le Parfait de 1 litro",
      "Color": "Color",
      "Orange": "Naranja",
      "Size": "Tamaño",
      "Medium": "Medio",
      "Weight": "Peso",
      "5Kg": "5Kg",
      "Modifier": "Modificador",
      "test2": "test2"
    }
  ],
  "en": [
    {
      "[Sample] 1 L Le Parfait Jar": "[Sample] 1 L Le Parfait Jar",
      "Color": "Color",
      "Orange": "Orange",
      "Size": "Size",
      "Medium": "Medium",
      "Weight": "Weight",
      "5Kg": "5Kg",
      "Modifier": "Modifier",
      "test2": "test2"
    }
  ]
}
If you want to write sample translation for cart name and cart configuration, you should add translations of carts as arrays.
```

## How to set up checkout type

```
There are 3 types of checkout type - `redirected`, `custom`, `embedded`.
If you set CHECKOUT_TYPE in env with these variables, it will be worked.
"redirected" - redirect to default checkout page provided in cart reidrect_urls.
"custom" - go to customized checkout page.
"embedded" - load default checkout page into our storefront checkout page as iframe. This wouldn't work on HTTP localhost. Firstly, you should use SSL for this using ngrok or any other thing. And you should input the https URL into channel site URL.
```

If you're new to BigCommerce, that's ok! You can create a free developer sandbox store here: https://developer.bigcommerce.com/sandbox/vue

## Props

Divante's awesome open sourced Storefront UI project made it possible to quickly make this look and feel like a real e-comm site using Vue + Nuxt. Check it out here: http://storefrontui.com/
