# Sales Assistant AI Tasks and Features

This document outlines the tasks and responsibilities for the sales assistant AI on the e-commerce website, utilizing the provided Apple product catalog.

## Core Tasks for the Sales Assistant

*   **Answering Product Questions**
    *   Retrieve and explain specific product specifications (e.g., "What is the screen size of the iPhone 16 Plus?").
    *   Clarify technical jargon for users (e.g., explaining the difference between the A18 Pro chip and the A18 chip).
    *   List available colors for a specific device.
        *   *Example based on data:* "The iPhone 16 Pro Max is available in Black Titanium, White Titanium, Natural Titanium, and Desert Titanium."
    *   Provide pricing information, including starting prices and variations based on storage capacity.
        *   *Example based on data:* "The MacBook Pro 14-inch (M4) starts at $1599, but upgrading from 512GB to 1TB or higher will increase the price."

*   **Product Selection and Recommendation**
    *   Conduct needs assessments by asking users what they plan to use the device for (e.g., photography, video editing, casual browsing).
    *   Compare two or more products to highlight differences and help the user decide.
        *   *Example based on data:* "If you want the largest screen and best battery life, I recommend the iPhone 16 Pro Max (6.9 inches). However, if you prefer a more compact Pro device, the iPhone 16 Pro (6.3 inches) has the same A18 Pro chip for a lower starting price of $999."
    *   Recommend upgrades based on user needs.
        *   *Example based on data:* "If you do heavy video editing, you might want to look at the MacBook Pro 16-inch with the M4 Pro or M4 Max chip rather than the MacBook Air 15-inch with the M3 chip."
    *   Suggest complementary accessories (cross-selling).
        *   *Example based on data:* "Since you are buying an iPhone 16, would you like to add the new AirPods 4 or AirPods Pro 2 (USB-C) for a seamless audio experience?"

*   **Guiding Through Checkout and Payments**
    *   Confirm the final product configuration (e.g., device model, color, storage capacity) before checkout.
    *   Provide a summary of the cart and the total estimated cost.
    *   Guide the user step-by-step through the shipping and billing information forms.
    *   Explain the available payment methods supported by the website.
    *   Assist with any errors encountered during the checkout process (e.g., "It looks like your zip code doesn't match the billing address, let's fix that.").

## Advice on Useful Features for the AI

Based on the tasks outlined above, the following features would be highly beneficial to build into the sales AI system:

*   **Contextual Memory:** The AI needs to remember what the user mentioned earlier in the conversation (e.g., "Since you mentioned earlier that you love taking photos, the 5x telephoto camera on the iPhone 15 Pro Max would be perfect.").
*   **Dynamic Comparison Tables:** A feature that allows the AI to generate and display side-by-side comparison tables (e.g., specs of iPhone 16 vs. iPhone 16 Pro) directly in the chat interface.
*   **Cart Management Integration:** The AI should have read/write access to the user's shopping cart, allowing it to add items directly (e.g., "I've added the Desert Titanium iPhone 16 Pro Max 256GB to your cart. Ready to checkout?").
*   **Image Rendering:** The ability to display product images in the chat based on the `image_url` in the data, so users can see the specific color they are asking about.
*   **Guided Checkout State Machine:** A structured conversational flow that specifically handles the checkout process, ensuring all required fields (shipping, billing, payment) are collected smoothly without overwhelming the user.
