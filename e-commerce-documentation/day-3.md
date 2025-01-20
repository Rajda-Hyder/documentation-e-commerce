# **Day 3 - API Integration Report - [MEUBEL HOUSE FURNITURE]**

---

## **Overview**
This document outlines the process of integrating an external API with Sanity.io and a Next.js application for "[MEUBEL HOUSE FURNITURE]". It details the steps followed, adjustments made, migration steps, tools used, and includes screenshots and code snippets to highlight the implementation.

---

## **API Integration Process**

### **Step 1: Setting Up Sanity.io**
#### 1. Installed the Sanity CLI globally:

   npm install -g @sanity/cli

#### 2. Initialized a new Sanity project:
   sanity init

- Project Name: figma-sanity
- Dataset: Production
- Schema Template: clear

#### 3. Deployed the schema to Sanity Studio:
    sanity deploy

#### 4. Started Sanity Studio to verify the setup:
     sanity start

### **Step 2: Defining the Product Schema**
1. Created a schema file product.ts with the following structure:
   
```typescript

export default {
  name: 'product',
  title: 'Product',
  type: 'document',
  fields: [
    { name: 'id', title: 'ID', type: 'string' },
    { name: 'name', title: 'Name', type: 'string' },
    { name: 'imagePath', title: 'Image Path', type: 'url' },
    { name: 'price', title: 'Price', type: 'number' },
    { name: 'description', title: 'Description', type: 'text' },
    { name: 'discountPercentage', title: 'Discount Percentage', type: 'number' },
    { name: 'isFeaturedProduct', title: 'Is Featured Product', type: 'boolean' },
    { name: 'stockLevel', title: 'Stock Level', type: 'number' },
    { name: 'category', title: 'Category', type: 'string' },
  ],
};
```

- Registered the schema in index.ts:  

```typescript
import { type SchemaTypeDefinition } from 'sanity'
import product from './product'

export const schema: { types: SchemaTypeDefinition[] } = {
  types: [product],
};
```

###**Step 3: Setting Up Next.js Project**

#### 1. I had already a next.js project.
#### 2. Installed required dependencies:
    
    npm install @sanity/client axios

### **Step 4: Fetching Data from API and Inserting into Sanity**
#### 4.1 Creating a Sanity Client
Created a file sanityClient.js with the following code:

```javascript
import { createClient } from '@sanity/client';

const client = createClient({
  projectId: process.env.NEXT_PUBLIC_SANITY_PROJECT_ID, // Public Sanity project ID
  dataset: process.env.NEXT_PUBLIC_SANITY_DATASET, // Public dataset name 
  useCdn: false, // `false` ensures fresh data; `true` caches data for faster reads
  token: process.env.SANITY_API_TOKEN, // Private Sanity API token
});
export default client;
``` 

#### 4.2 Creating an API Route to Insert Data
Added an API route in pages/api/fetch-and-insert.js:

```javascript
import axios from 'axios'; // For making HTTP requests
import client from "../../sanity/lib/sanityClient"; // Import Sanity client

export default async function handler(req, res) {
  try {
    // Check if data already exists in Sanity
    const existingProducts = await client.fetch(`*[_type == "product"]`);
    if (existingProducts.length > 0) {
      console.log("Data already exists in Sanity. Skipping API fetch...");
      return res.status(200).json({ message: "Data already exists in Sanity. No new data fetched." });
    }

    // Fetch data from the API
    console.log("Fetching data from API...");
    const { data } = await axios.get('https://template-0-beta.vercel.app/api/product');
    console.log("Fetched data:", data);

    // Insert each product into Sanity
    for (const product of data) {
      console.log("Inserting product:", product.name);
      await client.create({
        _type: 'product',
        id: product.id,
        name: product.name,
        imagePath: product.imagePath,
        price: parseFloat(product.price),
        description: product.description,
        discountPercentage: product.discountPercentage,
        isFeaturedProduct: product.isFeaturedProduct,
        stockLevel: product.stockLevel,
        category: product.category,
      });
    }

    res.status(200).json({ message: "Data fetched and inserted successfully!" });
  } catch (error) {
    console.error("Error occurred:", error);
    res.status(500).json({ error: "Failed to fetch or insert data" });
  }
}
   ```
  
### **4.3 Executing the API Route**
#### 1. Started the development server:

     npm run dev

#### 2.Accessed the API route to fetch and insert data:
     - URL: http://localhost:3000/api/fetch-and-insert

### **Step 5: Verifying the Data in Sanity**
#### 1. Opened the Sanity Studio in the browser (sanity start).
#### 2. Verified that the "Product" section in Sanity CMS was populated with the API data.
### **Step 6: Displaying Data in Next.js**
#### 6.1 Fetching Data from Sanity
In app/[shop], added the following code to fetch products and displayed the fetched data in the same file:

```typescript
import Image from "next/image";
import Link from "next/link";
import Foot from "../foot/page";
import { client } from "@/sanity/lib/client";
import Pagination from "../component/pagination";


interface Product {
  id: string;
  name: string;
  imagePath: string;
  price: number;
  description:string;
  discountPercentage:number;
  isFeaturedProduct:boolean;
  stockLevel:number;
  category:string;
}

export default async function Shop() {
  let products: Product[] = [];

  try {
    // Fetch all "product" documents from Sanity
    products = await client.fetch(`*[_type == "product"]`);
    console.log("Fetched products:", products);
  } catch (error) {
    console.error("Fetch failed with error:", error);
  }

  return (
    <div>
      {/* Shop Header */}
      <div className="relative bg-white w-full">
        <Image
          src="/assets/shopimages/shop1pic.png"
          alt="shop"
          width={300}
          height={300}
          className="w-full h-[316px]"
        />
        <ul className="absolute top-1/2 left-1/2 transform -translate-x-1/2 -translate-y-1/2">
          <li>
            <Image
              src="/assets/shopimages/shoplogo.png"
              alt="logo"
              width={100}
              height={100}
              className="-mt-20"
            />
          </li>
          <li className="flex font-poppins pb-20">
            <Link href="/">
              <span className="font-medium text-[16px] leading-[24px]">
                Home
              </span>
            </Link>
            <Image
              src="/assets/shopimages/shopSlogo.png"
              alt="small"
              width={50}
              height={50}
              className="w-[14px] h-[8px] m-2"
            />
            <Link href="/Shop">
              <span className="font-light text-[16px] leading-[24px]">
                Shop
              </span>
            </Link>
          </li>
        </ul>
        <Image
          src="/assets/shopimages/Group63.png"
          alt="panel"
          width={900}
          height={300}
          className="w-full h-[100px] my-10"
        />
      </div>
    

      {/* Shop Items */}
      
      <div>
      <section className="text-black font-poppins">
        <div className="container px-5 py-24 mx-auto">
          <div className="flex flex-wrap -m-4">
            {products.map((Product) => (
              <div key={Product.id} className="lg:w-1/4 md:w-1/2 p-4 w-full">
                <Link
                  href={`/Shop/${Product.id}`}
                  className="block relative h-48 rounded overflow-hidden"
                >
                  <Image
                    src={Product.imagePath}
                    alt={Product.name} // Use dynamic name for alt
                    width={400} // Set proper width
                    height={400} // Set proper height
                    className="object-contain object-center w-full h-full block"
                  />
                </Link>
                <div className="mt-4">
                  <h2 className="text-black title-font text-xl font-medium font-poppins">
                    {Product.name}
                  </h2>
                  <p className="mt-1 font-medium text-[16px]">
                    Category {Product.category}
                  </p>
                  <p className="mt-1 font-semibold text-[24px]">
                    Rs. {Product.price} <span className="font-normal text-[16px]">| Discount {Product.discountPercentage}%</span>
                  </p>

                  <p className="mt-1 font-normal text-[16px]">
                  Available Stock {Product.stockLevel}
                  </p>
                </div>
              </div>
            ))}
          </div>
        </div>
      </section>
    </div>
  
      {/* Pagination */}

      <Pagination />

      {/* Foot */}

      <Foot />

    </div>
  );
};
```
#### 6.2 Running the Application
1. Started the development server:
   npm run dev

2. Accessed the app at:
 - URL: http://localhost:3000
    
## **Adjustments Made to Schemas**

- Added the following fields to the product schema to support the API data:
  
    - id: To store the unique ID of the product from the API.
    - isFeaturedProduct: To mark whether a product is featured or not.
    - discountPercentage: To store discount information.
    - stockLevel: To manage product stock availability.
    - price:  To display product price.
    - description: To describe product features.
    - Category: To describe product category.
    - imagePath: To display product image.
    - name: to display product name.
  
## **Migration Steps and Tools Used**

 1. Created the schema file product.js in the Sanity project.
 2. Used the Sanity CLI to deploy the schema and migrate the data structure:
            sanity deploy
 3. Utilized the @sanity/client and axios packages in the Next.js project to:
     - Fetch data from the API.
     - Migrate and insert data into Sanity CMS.
  
## **Screenshots**
### 1. API Call 
This screenshot shows the API call response captured via browser.
it is second response thats why it shows this message.

![API Call Screenshot](./assets/apicall/Screenshot%20(254).png)

### 2. Populated Sanity CMS Fields
This screenshot shows how the API data has been successfully populated in the Sanity CMS.

![Sanity CMS Screenshot1](./assets/sanity/Screenshot%20(255).png)
![Sanity CMS Screenshot2](./assets/sanity/Screenshot%20(256).png)
![Sanity CMS Screenshot3](./assets/sanity/Screenshot%20(257).png)
![Sanity CMS Screenshot4](./assets/sanity/Screenshot%20(258).png)

### 3. Data Displayed on the Frontend
This screenshot displays the products fetched from Sanity CMS and rendered on the frontend.

![Frontend Product Display](./assets/frontend/Screenshot%20(259).png)
![Frontend Product Display](./assets/frontend/Screenshot%20(260).png)
![Frontend Product Display](./assets/frontend/Screenshot%20(261).png)
![Frontend Product Display](./assets/frontend/Screenshot%20(262).png)

## **Code Snippets**
My code snippets are provided alongside each step above.

## **Conclusion**
This document provides a step-by-step guide to integrate an external API with Sanity.io and Next.js. The API data was successfully fetched, inserted into Sanity CMS, and dynamically displayed on the frontend.








   






   







