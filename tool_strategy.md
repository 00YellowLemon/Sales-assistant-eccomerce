# LangChain Tool Strategy for Sales Assistant AI

This document outlines the tools required for the Sales Assistant AI, designed to be built with LangChain and integrated with a Firestore database containing Apple products. The strategy includes a mix of comprehensive search tools and fine-grained detail tools to provide the agent with maximum flexibility.

## 1. Product Search & Discovery (Comprehensive Tools)

These tools are designed to query the Firestore database broadly based on user needs.

### `search_products_by_attributes`
*   **Description:** A comprehensive search tool to find products based on multiple criteria such as category, maximum price, specific features (like screen size or processor), or keywords.
*   **Input:**
    *   `category` (string, optional): e.g., "Phone", "Laptop", "Earbuds".
    *   `max_price` (integer, optional): Maximum starting price.
    *   `keywords` (list of strings, optional): Keywords from the user's prompt (e.g., ["video editing", "large screen"]).
    *   `release_year` (integer, optional): Specific release year to filter by.
*   **Output:** A list of product dictionaries matching the criteria, containing basic info (name, category, starting price, description).

### `recommend_products_by_use_case`
*   **Description:** Takes a natural language description of what the user wants to do and returns recommended products. Useful for needs assessment.
*   **Input:**
    *   `use_case_description` (string, required): e.g., "I need a laptop for heavy video editing" or "I want the best camera phone".
*   **Output:** A list of recommended product names and a brief reasoning for the recommendation based on the product descriptions and specs.

## 2. Product Details (Fine-Grained Tools)

These tools allow the agent to fetch specific pieces of information quickly without retrieving the entire product document, useful for answering direct questions.

### `get_product_price`
*   **Description:** Retrieves the starting price and pricing variations for different storage capacities of a specific product.
*   **Input:**
    *   `product_name` (string, required): The exact or close match name of the product (e.g., "iPhone 16 Pro Max").
*   **Output:** A dictionary containing `starting_price` and a mapping of `storage_capacities` to their respective prices (if applicable in the schema).

### `get_product_colors`
*   **Description:** Retrieves the available colors and their corresponding image URLs for a specific product.
*   **Input:**
    *   `product_name` (string, required): The exact or close match name of the product.
*   **Output:** A list of dictionaries, each containing `color` name and `image_url`.

### `get_product_specs`
*   **Description:** Retrieves the technical specifications (screen size, processor chip, storage options) for a specific product.
*   **Input:**
    *   `product_name` (string, required): The exact or close match name of the product.
*   **Output:** A dictionary containing the `specs` object from the database.

### `compare_products`
*   **Description:** Fetches the specs and details of two or more products to facilitate side-by-side comparison.
*   **Input:**
    *   `product_names` (list of strings, required): A list of product names to compare (e.g., ["iPhone 16 Pro", "iPhone 16"]).
*   **Output:** A structured dictionary comparing the features (price, screen size, processor, etc.) of the requested products.

## 3. Cart Management

These tools give the AI read/write access to the user's shopping session.

### `add_to_cart`
*   **Description:** Adds a specific product configuration to the user's shopping cart.
*   **Input:**
    *   `product_name` (string, required): The name of the product.
    *   `color` (string, required): The chosen color.
    *   `storage_capacity` (string, optional): The chosen storage capacity (if applicable).
    *   `quantity` (integer, optional): Default is 1.
*   **Output:** A success message confirming the item was added, or an error if the configuration is invalid.

### `view_cart`
*   **Description:** Retrieves the current contents of the user's shopping cart.
*   **Input:** None (relies on session context).
*   **Output:** A list of items in the cart, including product name, configuration, quantity, individual price, and the total estimated cost.

### `remove_from_cart`
*   **Description:** Removes an item from the user's shopping cart.
*   **Input:**
    *   `item_id` or `product_name` (string, required): Identifier for the item to remove.
*   **Output:** A success message updating the cart status.

## 4. Checkout & Guidance

These tools handle the final stages of the customer journey, prioritizing security by handing off sensitive processes to the UI.

### `generate_checkout_link`
*   **Description:** Generates a secure URL or UI trigger for the user to complete their purchase, passing along the finalized cart details.
*   **Input:**
    *   `cart_id` or `session_id` (string, required): Identifier for the current user's session/cart.
*   **Output:** A string containing the URL to the secure checkout page, which the AI will present to the user.

### `get_checkout_requirements`
*   **Description:** Retrieves the list of information the user needs to have ready for checkout (e.g., shipping address format, accepted payment methods). Useful for guiding the user before generating the link.
*   **Input:** None.
*   **Output:** A list of accepted payment methods and required shipping/billing fields.
