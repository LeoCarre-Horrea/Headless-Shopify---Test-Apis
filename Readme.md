# Shopify Aperol Demo – README

## Purpose

This site was created to demonstrate the feasibility of:

- Retrieving Shopify products based on market (country/language)
- Filtering products by collection
- Dynamically displaying prices, currencies, languages, and product links according to the selected market
- Showing that the Shopify Storefront API allows you to obtain all this information, contrary to what some providers may claim

---

## Main Features

- **Market Selector (country/language):** Choose the display context (e.g., Germany/German, Belgium/French, UK/English, etc.)
- **Collection Filtering:** Display only products from a selected collection
- **Product Search:** Real-time filtering of products by name
- **Dynamic Price & Currency Display:** Prices are shown in the correct currency and format for the selected market (€, £, etc.)
- **Product ID Display:** The numeric Shopify product ID is extracted and shown
- **Product Slider/Carousel:** Visualize products in a slider format
- **Dynamic Product Links:** Links point to the correct Shopify URL for the selected market/language

---

## Technical Overview

### 1. Market Selector

- The `<select id="marketSelector">` offers a list of markets, each value matching a key in the `markets` JS object.
- Example: `BE-fr` for Belgium/French, `UK-en` for UK/English, etc.
- When a market is selected, the JS code uses the key to:
  - Set the context for GraphQL queries (`country` and `language`)
  - Adapt product URLs (e.g., `/fr-be/`, `/en-gb/`, ...)

### 2. Shopify Storefront API Calls

- Uses the Storefront API (GraphQL):  
  Endpoint: `https://<shop>.myshopify.com/api/2024-04/graphql.json`
- Authentication via a Storefront Access Token
- Queries are made with country/language context using the `@inContext(country: $country, language: $language)` directive
- Example queries:
  - **All Products:**
    ```graphql
    query getProducts($country: CountryCode, $language: LanguageCode)
    @inContext(country: $country, language: $language) {
      products(first: 250) {
        edges {
          node {
            id
            title
            handle
            images(first: 1) {
              edges {
                node {
                  src
                  altText
                }
              }
            }
            variants(first: 1) {
              edges {
                node {
                  price {
                    amount
                    currencyCode
                  }
                }
              }
            }
          }
        }
      }
    }
    ```
  - **Products by Collection:**
    ```graphql
    query getCollectionProducts(
      $handle: String!
      $country: CountryCode
      $language: LanguageCode
    ) @inContext(country: $country, language: $language) {
      collectionByHandle(handle: $handle) {
        products(first: 250) {
          edges {
            node {
              id
              title
              handle
              images(first: 1) {
                edges {
                  node {
                    src
                    altText
                  }
                }
              }
              variants(first: 1) {
                edges {
                  node {
                    price {
                      amount
                      currencyCode
                    }
                  }
                }
              }
            }
          }
        }
      }
    }
    ```
  - **List Collections:**
    ```graphql
    query getCollections($country: CountryCode, $language: LanguageCode)
    @inContext(country: $country, language: $language) {
      collections(first: 50) {
        edges {
          node {
            id
            title
            handle
          }
        }
      }
    }
    ```

### 3. Dynamic Display

- Products are stored client-side and filtered in JS for search.
- Prices are formatted according to the market's currency:
  - Euro, Pound Sterling, etc.
  - For GBP, only the symbol '£' is shown.
- Product links are generated dynamically to point to the correct language/market on the Shopify site.

### 4. Best Practices

- **Use the Storefront API** to get contextualized data (country/language): this is the official and recommended Shopify method.
- **Keep market/language mapping in sync**: always match the select values with the JS object keys.
- **Extract the numeric product ID** from the Shopify GID for display or export.
- **Adapt product URLs** to Shopify's multilingual convention (e.g., `/fr-be/`, `/en-gb/`, ...).
- **Filter client-side** for fast search, but always query the API for collection or market changes.
- **Display prices in the correct format** (use `toLocaleString` with the right locale and currency).

---

## How to Use/Modify This Project

### Setup Instructions

1. **Clone the project**
2. **Open the `Slider.html` file in a browser**
3. **Configure the Storefront Access Token and Shopify domain** if needed (at the top of the script)
4. **Adapt the list of markets** in JS and HTML if you have other countries/languages
5. **Customize the style or features** as needed

6. **Configure your Shopify credentials:**

   - Open `Headless-app.html` (or `Slider.html`)
   - Locate the JavaScript variables at the top of the script:
     ```javascript
     const storefrontAccessToken = "YOUR_STOREFRONT_ACCESS_TOKEN";
     const shopDomain = "YOUR_SHOP.myshopify.com";
     ```
   - Replace with your actual Shopify Storefront Access Token and shop domain

7. **Start a local server:**

   - **Option 1 - Live Server (VS Code):** Install the "Live Server" extension and right-click on `Headless-app.html` → "Open with Live Server"
   - **Option 2 - Python:** Run `python -m http.server 8000` in the project directory, then visit `http://localhost:8000/Headless-app.html`
   - **Option 3 - Node.js:** Install `http-server` globally (`npm install -g http-server`) and run `http-server` in the project directory
   - **Option 4 - GitHub Pages:** Push your code to a GitHub repository and enable GitHub Pages in the repository settings

8. **Access the application:**
   - Open your browser and navigate to the local server URL
   - The application should now display products from your Shopify store

### Configuration Details

- **Storefront Access Token:** Generate this in your Shopify admin under Apps → App and sales channel settings → Storefront API → Configure → Storefront API access tokens
- **Shop Domain:** Your Shopify store URL (e.g., `your-store.myshopify.com`)
- **CORS Issues:** If you encounter CORS errors, ensure you're running the application through a web server (not just opening the HTML file directly)

### Customization Options

1. **Adapt the list of markets** in JS and HTML if you have other countries/languages
2. **Customize the style or features** as needed
3. **Modify the product display format** by editing the CSS and HTML templates

---

## Key Points for Providers

- **It is absolutely possible** to retrieve products, prices, languages, collections, etc. via the Shopify Storefront API for any market/language.
- **The official Shopify documentation** explains how to use the `@inContext` directive to get localized data.
- **Filtering by collection** and retrieving localized prices are native to the API.
- **Multilingual URL management** is just a matter of front-end mapping.

---

## Useful Links

- [Shopify Storefront API Documentation](https://shopify.dev/docs/api/storefront)
- [GraphQL Query Examples](https://shopify.dev/docs/api/storefront/queries/products)
- [Internationalization: Markets & Languages](https://shopify.dev/docs/api/storefront/internationalization)

---

Feel free to ask for details at leo.carre@horrea.fr
