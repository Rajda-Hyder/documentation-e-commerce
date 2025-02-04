# Day 4 - Dynamic Frontend Components - [MEUBEL HOUSE FURNITURE]

## 1. Functional Deliverables
### 1.1 Product Listing Page with Dynamic Data
✅ Screenshot of the Product Listing Page displaying data fetched dynamically.

![Product Listning page](./productListningPage/Screenshot%20(266).png)

### 1.2 Individual Product Detail Pages with Dynamic Data
✅ Screenshot of the Product Detail Page with accurate routing and product data.

![Product Detail Page](./assets/ProductDetailPage/Screenshot%20(269).png)

### 1.3 Working Category Filters, Search Bar, and Pagination
✅ Screenshot of:

- Category Filters 
- Search Bar in Action 
- Pagination Feature 

#### Category Filters
![defaul](./productListningPage/Screenshot%20(266).png)
![Open Category Filter](./assets/CategoryFilter/Screenshot%20(267).png)
![Price (Low to High)](./assets/CategoryFilter/Screenshot%20(279).png)
![Stock (Low to High)](./assets/CategoryFilter/Screenshot%20(280).png)
![Category A-Z](./assets/CategoryFilter/Screenshot%20(268).png)

#### Search Bar in Action
You can display as many products as you want,if you set the display to show 3 products, only 3 products will be shown.

![3 products](./assets/searchBar/Screenshot%20(281).png)
![8 products](./assets/searchBar/Screenshot%20(282).png)
![12 products](./assets/searchBar/Screenshot%20(284).png)

#### Pagination Feature
if you set less product to show pagination number will be increase or you set more product, pagination number will decrease. 

![3 products pagination increase in 7 pages](./assets/searchBar/Screenshot%20(281).png)
![8 products pagination increase in 7 pages](./assets/Pagination/Screenshot%20(286).png)
![12 products pagination increase in 7 pages](./assets/Pagination/Screenshot%20(285).png)

### 1.4 Additional Features Implemented

#### Related Product on Product Detail Page with Dynamic Data
✅ Screenshot of the Related Product with accurate routing and product data.when clicked show on product detail page.

![Related Product Under product Detail with same category](./assets/ProductDetailPage/RelatedProduct/Screenshot%20(270).png)

#### Make Cart DropDown functional
✅ Screenshot of the Cart DropDown with product in it dynamically.when no product selected dropdown is empty with "Your Cart is Empty".

![product selected](./assets/dropdown/Screenshot%20(272).png)
![Empty cart](./assets/dropdown/Screenshot%20(287).png)

## 2. Code Deliverables

### 2.1 Key Component Code Snippets

#### 1- [shop]/page.tsx
```typescript
"use client";

import { useState, useEffect } from "react";
import Image from "next/image";
import Link from "next/link";
import Foot from "../foot/page";
import { client } from "@/sanity/lib/client";
import Pagination from "../component/pagination";
import FilterBar from "../component/filterBar";

// Define the Product Type
interface Product {
  _id:string;
  id: string;
  name: string;
  imagePath: string;
  price: number;
  description: string;
  discountPercentage: number;
  isFeaturedProduct: boolean;
  stockLevel: number;
  category: string;
}

export default function Shop() {
  // States for Products and Sorting
  const [products, setProducts] = useState<Product[]>([]); // Original Products
  const [sortedProducts, setSortedProducts] = useState<Product[]>([]); // Sorted Products
  const [short, setShort] = useState("default"); // Sorting option
  const [results, setResults] = useState(0); // Total Results
  const [show, setShow] = useState(8); // Items to show per page
  const [currentPage, setCurrentPage] = useState(1); // Current page number

  // Fetch Products from Sanity
  useEffect(() => {
    const fetchProducts = async () => {
      try {
        const fetchedProducts = await client.fetch<Product[]>(
          `*[_type == "product"]`
        );
        setProducts(fetchedProducts); // Set Original Products
        setSortedProducts(fetchedProducts); // Set Initial Sorted Products
        setResults(fetchedProducts.length); // Set Total Results
      } catch (error) {
        console.error("Error fetching products:", error);
      }
    };

    fetchProducts();
  }, []);

  // Handle Sorting Logic
  const handleSort = (option: string) => {
    setShort(option); // Update Selected Sort Option
    let sortedArray = [...products]; // Copy Original Products

    if (option === "price") {
      sortedArray.sort((a, b) => a.price - b.price); // Sort by Price
    } else if (option === "stock") {
      sortedArray.sort((a, b) => a.stockLevel - b.stockLevel); // Sort by Stock
    } else if (option === "category") {
      sortedArray.sort((a, b) =>
        a.category.localeCompare(b.category) // Sort Alphabetically by Category
      );
    } else {
      sortedArray = [...products]; // Default Order
    }

    setSortedProducts(sortedArray); // Update Sorted Products
  };

  // Pagination Logic
  const indexOfLastProduct = currentPage * show;
  const indexOfFirstProduct = indexOfLastProduct - show;
  const currentProducts = sortedProducts.slice(
    indexOfFirstProduct,
    indexOfLastProduct
  );

  const paginate = (pageNumber: number) => setCurrentPage(pageNumber);

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
              <span className="font-light text-[16px] leading-[24px]">Shop</span>
            </Link>
          </li>
        </ul>

        {/* FilterBar Component */}
        <FilterBar
          short={short}
          setShort={setShort}
          handleSort={handleSort}
          results={results}
          show={show}
          setShow={setShow}
        />
      </div>

      {/* Shop Items */}
      <div>
        <section className="text-black font-poppins">
          <div className="container px-5 py-24 mx-auto">
            <div className="flex flex-wrap -m-4">
              {currentProducts.map((product) => (
                <div
                  key={product.id}
                  className="lg:w-1/4 md:w-1/2 p-4 w-full"
                >
                  <Link
                    href={`/Shop/${product.id}`}
                    className="block relative h-48 rounded overflow-hidden"
                  >
                    <Image
                      src={product.imagePath}
                      alt={product.name}
                      width={400}
                      height={400}
                      className="object-contain object-center w-full h-full block"
                    />
                  </Link>
                  <div className="mt-4">
                    <h2 className="text-black title-font text-xl font-medium font-poppins">
                      {product.name}
                    </h2>
                    <p className="mt-1 font-medium text-[16px]">
                      Category: {product.category}
                    </p>
                    <p className="mt-1 font-semibold text-[24px]">
                      Rs. {product.price}{" "}
                      <span className="font-normal text-[16px]">
                        | Discount {product.discountPercentage}%
                      </span>
                    </p>

                    <p className="mt-1 font-normal text-[16px]">
                      Available Stock: {product.stockLevel}
                    </p>
                  </div>
                </div>
              ))}
            </div>
          </div>
        </section>
      </div>

      {/* Pagination Component */}
      <Pagination 
  currentPage={currentPage} 
  totalResults={results} 
  resultsPerPage={show} 
  paginate={paginate} 
/>

      {/* Footer */}
      <Foot />
    </div>
  );
}
```
#### 2-[id]/page.tsx

```typescript
// app/shop/[id]/page.tsx (Server Component)
import { Metadata } from 'next';
import { client } from "@/sanity/lib/client";
import ProductDetailClient from "@/app/component/productdetailclient"; // Import the Client Component

interface Product {
  _id: string;
  id: string;
  name: string;
  imagePath: string;
  price: number;
  description: string;
  stockLevel: number;
  category: string;
}

interface PageProps {
  params: { id: string };
}

// Generate Metadata Dynamically
export async function generateMetadata({ params }: PageProps): Promise<Metadata> {
  const { id } =await params;

  // Fetch product data for metadata
  const product = await client.fetch<Product>(
    `*[_type == "product" && id == $id][0]`,
    { id }
  );

  return {
    title: product ? `${product.name} - Product Details` : "Product Not Found",
    description: product?.description || "No description available.",
    openGraph: {
    images: product?.imagePath? [product.imagePath] : [],
    },
    };
   }
   

// Main Page Component
export default async function ProductPage({ params }: PageProps) {
  const { id } = params;

  // Fetch product data
  const product = await client.fetch<Product>(
    `*[_type == "product" && id == $id][0]`,
    { id }
  );

  if (!product) {
    return <p>Product not found</p>;
  }

// Pass product data to the Client Component
return <ProductDetailClient product={product} />;
}
```
#### 3-ProductDetailClient.tsx

```typescript
"use client"
import Image from "next/image";
import Link from "next/link";
import svg from "../../../public/assets/icons/star-half-icon.png";
import fb from "../../../public/assets/icons/fb.png";
import link from "../../../public/assets/icons/link.png";
import twit from "../../../public/assets/icons/twit.png";
import rh from "../../../public/assets/icons/redheart.png";
import arrow from "../../../public/assets/icons/arrow.png";
import line from "../../../public/assets/icons/Line5.png";
import React from "react"; // Import React to use React.use()
import { useCart } from "@/app/contexts/cartContext";
import RelatedProducts from "./[homesection]/relatedProduct";

interface Product {
  id: string;
  name: string;
  imagePath: string;
  price: number;
  category: string;
  description:string;
}


export default function ProductDetailClient({ product }: { product: Product}) {
  console.log("product data:", product)
  const { dispatch } = useCart(); // Use the cart context

  // Add to Cart Function
  const addToCart = () => {
    dispatch({
      type: "ADD_TO_CART",
      payload: {
        _id:product.id,
        id: product.id,
        name: product.name,
        price: product.price,
        quantity: 1,
        imagePath: product.imagePath,
        
      },
    });
  };

  // Render product details
  return (
    <div className="mt-1">
      {/* Breadcrumb Section */}
      <div className="p-8 ml-4 sm:ml-10 md:ml-24">
        <ul className="flex font-poppins font-normal gap-2 sm:gap-4 text-[#9F9F9F]">
          <li><Link href="/">Home</Link></li>
          <li className="font-bold">
            <Image src={arrow} alt="arrow" />
          </li>
          <li><Link href="/shop">Shop</Link></li>
          <li className="text-black">
            <Image src={arrow} alt="arrow" />
          </li>
          <li>
            <Image src={line} alt="line" />
          </li>
          <li className="text-black">Product Detail</li>
        </ul>
      </div>

      
        <div  className="space-y-10">
          {/* Product Main Section */}
          <div className="flex flex-col md:flex-row gap-10 md:gap-20 px-4 sm:px-10 md:px-24">
            {/* Image Grid Section */}
            <div className="space-y-4">
            <div className="flex flex-col md:flex-row gap-6">
  {/* Thumbnail Column */}
  <ul className="flex flex-row md:flex-col gap-14 justify-center">
    <li>
      <Image
        src={product.imagePath}
        alt={product.name}
        width={100}
        height={100}
        className="object-contain w-auto h-auto"
      />
    </li>
    <li>
      <Image
        src={product.imagePath}
        alt={product.name}
        width={100}
        height={100}
        className="object-contain w-auto h-auto"
      />
    </li>
    <li>
      <Image
        src={product.imagePath}
        alt={product.name}
        width={100}
        height={100}
        className="object-contain w-auto h-auto"
      />
    </li>
    <li>
      <Image
        src={product.imagePath}
        alt={product.name}
        width={100}
        height={100}
        className="object-contain w-auto h-auto"
      />
    </li>
  </ul>

  {/* Main Image */}
  <Image
    src={product.imagePath}
    alt={product.name}
    width={500}
    height={500}
    className="object-contain w-[500] max-w-lg mx-auto"
  />
</div>
</div>
            {/* Product Information Section */}
            <div className="font-poppins font-normal space-y-6 w-full md:w-1/2">
              {/* Product Name */}
              <h1 className="text-gray-900 text-3xl">{product.name}</h1>

              {/* Product Price */}
              <h3 className="text-xl font-medium">Rs. {product.price}</h3>

              {/* Rating */}
              <div className="flex items-center space-x-2">
                {[...Array(4)].map((_, index) => (
                  <svg
                    key={index}
                    fill="currentColor"
                    stroke="currentColor"
                    strokeLinecap="round"
                    strokeLinejoin="round"
                    strokeWidth={2}
                    className="w-5 h-5 text-[#FFDA5B]"
                    viewBox="0 0 24 24"
                  >
                    <path d="M12 2l3.09 6.26L22 9.27l-5 4.87 1.18 6.88L12 17.77l-6.18 3.25L7 14.14 2 9.27l6.91-1.01L12 2z" />
                  </svg>
                ))}
                <Image src={svg} alt="Rating Icon" width={20} height={20} />
                <span className="text-[#9F9F9F]">5 Customer Reviews</span>
              </div>

              {/* Product Description */}
              <p className="text-gray-600 text-sm leading-relaxed">
                {product.description}
              </p>

              {/* Size Options */}
              <div className="space-y-2">
                <h4 className="font-medium text-[#9F9F9F]">Size</h4>
                <div className="flex space-x-2">
                  {["L", "XL","XS"].map((size) => (
                    <button
                      key={size}
                      className="w-[30px] h-[30px] border rounded-md text-gray-600 hover:bg-[#FBEBB5] transition duration-300 ease-in-out"
                    >
                      {size}
                    </button>
                  ))}
                </div>
              </div>

              {/* Color Options */}
              <div className="space-y-2">
                <h4 className="font-medium">Color:</h4>
                <div className="flex space-x-3">
                  <button className="w-6 h-6 bg-[#816DFA] border rounded-full"></button>
                  <button className="w-6 h-6 bg-black border rounded-full"></button>
                  <button className="w-6 h-6 bg-[#CDBA7B] border rounded-full"></button>
                </div>
              </div>

              
        {/* Add to Cart Button */}
       <Link href="/cart">         <button
          onClick={addToCart} // Add to cart when button is clicked
          className="bg-blue-500 text-white py-2 px-4 rounded mt-4"
        >
          Add to Cart
        </button>
</Link>
           </div>
          </div>

          {/* Product Meta Section */}
          <div className="px-4 sm:px-10 md:px-24 space-y-4">
              {/* Line above the section */}
  <div className="border-t border-gray-300 w-1/2 ml-auto mb-16"></div> {/* Horizontal line */}
  
  {/* Meta content aligned to the right */}

            <div className="flex justify-end mr-64 space-x-6 mb-4">
              <ul className="space-y-2 text-[#9F9F9F]">
                <li>SKU</li>
                <li>Category</li>
                <li>Tags</li>
                <li>Share</li>
              </ul>
              <ul className="space-y-2 text-[#9F9F9F]">
                <li>:</li>
                <li>:</li>
                <li>:</li>
                <li>:</li>
              </ul>
              <ul className="space-y-2">
                <li>SS001</li>
                <li>Sofas</li>
                <li>Sofa, Chair, Home, Shop</li>
                <li className="flex space-x-3">
                  <Image src={fb} alt="Facebook" width={20} height={20} />
                  <Image src={link} alt="LinkedIn" width={20} height={20} />
                  <Image src={twit} alt="Twitter" width={20} height={20} />
                  <div className="ml-8">
                  <Image src={rh} alt="Red Heart" width={25} height={32}/>
                  </div>
                </li>
              </ul>
            </div>
          </div>
<div className="flex justify-center">
                  {/* Related Products Component */}
      <RelatedProducts currentCategory={product.category} currentProductId={product.id} />

          </div>

          {/* View More Button */}
          <div className="flex justify-center py-10">
            <Link href="/shop">
              <button className="font-poppins font-semibold text-base underline">
                View More
              </button>
            </Link>
          </div>
        </div>
    </div>
  
  );
}
```
#### 4-RelatedProducts.tsx

```typescript
import { useEffect, useState } from "react";
import Image from "next/image";
import Link from "next/link";
import { client } from "@/sanity/lib/client";

interface Product {
  _id:string;
  id: string;
  name: string;
  imagePath: string;
  price: number;
  category: string;
}


export default function RelatedProducts({ currentCategory, currentProductId }: { currentCategory: string; currentProductId: string }) {
  const [relatedProducts, setRelatedProducts] = useState<Product[]>([]);

  useEffect(() => {
    const fetchRelatedProducts = async () => {
      try {
        //  Fetch products that match the category and exclude the current product
        const fetchedProducts = await client.fetch<Product[]>(
          `*[_type == "product" && category == $category && _id != $id]{
            _id,
            id,
            name,
            imagePath,
            price,
            category
          }`,
          { category: currentCategory, id: currentProductId }
        );

        setRelatedProducts(fetchedProducts);
      } catch (error) {
        console.error("Error fetching related products:", error);
      }
    };

    fetchRelatedProducts();
  }, [currentCategory, currentProductId]);

  if (relatedProducts.length === 0) {
    return null; // No related products, so don't display the section
  }

  return (
    <div className="mt-10">
      <h2 className="text-4xl font-bold my-10 font-poppins text-center justify-center">Related Products</h2>
      <div className="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 lg:grid-cols-4 gap-6">
        {relatedProducts.map((product) => (
          <div key={product.id} className="border p-4 rounded-lg shadow-md">
            <Link href={`/Shop/${product.id}`}>
              <Image src={product.imagePath} 
              alt={product.name} 
              width={200} 
              height={200} 
              className="object-contain w-full h-40" />
              <h3 className="mt-2 text-lg font-medium">{product.name}</h3>
              <p className="text-gray-600">Rs. {product.price}</p>
              <p className="text-gray-600">{product.category}</p>
            </Link>
          </div>
        ))}
      </div>
    </div>
  );
}
```
#### 5-FilterBar.tsx

```typescript
"use client";
import Image from "next/image";
import React from "react";
import icon from "../../../public/assets/shopimages/system-uicons_filtering.png";
import icon1 from "../../../public/assets/shopimages/ci_grid-big-round.png";
import icon2 from "../../../public/assets/shopimages/bi_view-list.png";
import icon3 from "../../../public/assets/shopimages/Line 5.png";

// Define Props Interface
interface FilterBarProps {
  short: string; // Currently selected sorting option
  setShort: (value: string) => void; // Function to update sorting option
  handleSort: (option: string) => void; // Function to handle sorting logic
  results: number; // Total number of results
  show: number; // Items to show per page
  setShow: (value: number) => void; // Function to update items to show per page
}

const FilterBar: React.FC<FilterBarProps> = ({
  short,
  setShort,
  handleSort,
  results,
  show,
  setShow,
}) => {
  return (
    <div className="filter-bar flex justify-between items-center p-6 bg-[#FAF4F4] mt-10">
      {/* Filter Button */}
      <button className="flex items-center space-x-2 ml-20 gap-2">
        <span className="material-icons">
          <Image src={icon} alt="icon" />
        </span>
        <span className="text-[24px] ml-8">Filter</span>
        <div className="flex items-center gap-4">
          <span className="mx-4">
            <Image src={icon1} alt="icon1" />
          </span>
          <span className="ml-4">
            <Image src={icon2} alt="icon2" />
          </span>
          <span className="ml-4">
            <Image src={icon3} alt="icon3" />
          </span>
        </div>
      </button>

      {/* Showing Results */}
      <div>
        Showing  1-{show}  of  {results} results
      </div>

      {/* Show Dropdown */}
      <div className="flex items-center space-x-2">
        <span className="ml-80">Show</span>
        <input
          type="number"
          value={show}
          onChange={(e) => setShow(Number(e.target.value))}
          className="border rounded w-12 text-center"
        />
      </div>

      {/* Sort Dropdown */}
      <div className="flex items-center space-x-2 mr-20">
        <span>Short by</span>
        <select
          value={short}
          onChange={(e) => {
            setShort(e.target.value); // Update sorting option
            handleSort(e.target.value); // Call sorting logic
          }}
          className="border rounded p-1"
        >
          <option value="default">Default</option>
          <option value="price">Price (Low to High)</option>
          <option value="stock">Stock (Low to High)</option>
          <option value="category">Category (A-Z)</option>
        </select>
      </div>
    </div>
  );
};

export default FilterBar;
```
#### 6-Pagination.tsx

```typescript
import Link from "next/link";

interface PaginationProps {
  currentPage: number;
  totalResults: number;
  resultsPerPage: number;
  paginate: (pageNumber: number) => void;
}

export default function Pagination({ currentPage, totalResults, resultsPerPage, paginate }: PaginationProps) {
  const pageNumbers = [];

  // Calculate total pages
  const totalPages = Math.ceil(totalResults / resultsPerPage);

  for (let i = 1; i <= totalPages; i++) {
    pageNumbers.push(i);
  }

  return (
    <div className="flex flex-row justify-center py-28 gap-[38px]">
      {pageNumbers.map((number) => (
        <button
          key={number}
          onClick={() => paginate(number)}
          className={`w-[60px] h-[60px] ${
            currentPage === number ? "bg-[#FBEBB5]" : "bg-[#FFF9E5]"
          } text-black rounded-sm flex items-center justify-center hover:bg-[#FBEBB5] transition duration-300 ease-in-out`}
        >
          {number}
        </button>
        
      ))}
      <div><Link href="#">
        <button className="w-[98px] h-[60px] bg-[#FFF9E5] text-black rounded-lg flex items-center justify-center hover:bg-[#FBEBB5] transition duration-300 ease-in-out">
          Next
        </button>
      </Link>
</div>
    </div>
  );
}
```

#### 7-CartContext.tsx

```typescript
"use client"
// contexts/CartContext.tsx
import React, { createContext, useContext, useReducer, ReactNode } from "react";

// Define the product type
interface Product {
  _id:string;
  id: string;
  name: string;
  price: number;
  quantity: number;
  imagePath: string;
}

// Define the cart state and actions
interface CartState {
  cart: Product[];
}

type Action =
  | { type: "ADD_TO_CART"; payload: Product }
  | { type: "REMOVE_FROM_CART"; payload: string }
  | { type: "UPDATE_QUANTITY"; payload: { id: string; quantity: number } };
  
// Create the CartContext
const CartContext = createContext<{
  state: CartState;
  dispatch: React.Dispatch<Action>;
} | null>(null);

// Reducer function
const cartReducer = (state: CartState, action: Action): CartState => {
  switch (action.type) {
    case "ADD_TO_CART":
      const productExists = state.cart.find(
        (item) => item.id === action.payload.id
      );

      if (productExists) {
        return {
          cart: state.cart.map((item) =>
            item.id === action.payload.id
              ? { ...item, quantity: item.quantity + 1 }
              : item
          ),
        };
      } else {
        return { cart: [...state.cart, { ...action.payload, quantity: 1 }] };
      }
    case "REMOVE_FROM_CART":
      return {
        cart: state.cart.filter((item) => item.id !== action.payload),
      };
    case "UPDATE_QUANTITY":
      return {
        cart: state.cart.map((item) =>
          item.id === action.payload.id
            ? { ...item, quantity: action.payload.quantity }
            : item
        ),
      };
    default:
      return state;
  }
};

// Define the props type for CartProvider
interface CartProviderProps {
  children: ReactNode; // Explicitly type children
}

export const CartProvider: React.FC<CartProviderProps> = ({ children }) => {
  const [state, dispatch] = useReducer(cartReducer, { cart: [] });

  return (
    <CartContext.Provider value={{ state, dispatch }}>
      {children}
    </CartContext.Provider>
  );
};

// Hook to use CartContext
export const useCart = () => {
  const context = useContext(CartContext);
  if (!context) {
    throw new Error("useCart must be used within a CartProvider");
  }
  return context;
};
```

#### 8-CartProvider.tsx

```typescript
"use client";

import { CartProvider } from "../contexts/cartContext";

export default function CartProviderWrapper({
  children,
}: {
  children: React.ReactNode;
}) {
  return <CartProvider>{children}</CartProvider>;
}
```

#### 9-Dropdown/page.tsx

```typescript
"use client";
import React, { useState } from "react";
import Image from "next/image";
import Link from "next/link";
import cart1 from "../../../public/assets/cart/ant-design.png";
import lock from "../../../public/assets/cart/lock.png";
import cross from "../../../public/assets/cart/cross.png";
import { useCart } from "../contexts/cartContext"; // Import Cart Context

export default function CartDropdown() {
  const [isOpen, setIsOpen] = useState(false); // State for dropdown visibility
  const { state, dispatch } = useCart(); // Access Cart State and Dispatch
  const { cart } = state; // Extract cart items

  // Toggle dropdown visibility
  const toggleDropdown = () => {
    setIsOpen(!isOpen);
  };

  // Remove item from cart
  const removeFromCart = (id: string) => {
    dispatch({ type: "REMOVE_FROM_CART", payload: id });
  };

  // Calculate total price
  const subtotal = cart.reduce(
    (total, item) => total + item.price * item.quantity,
    0
  );

  return (
    <div className="relative inline-block text-left font-poppins">
      {/* Cart Icon Button */}
      <button
        onClick={toggleDropdown}
        className="flex items-center justify-center rounded-lg transition duration-300"
      >
        <Image src={cart1} alt="Cart" width={30} height={30} className="w-[28px] h-[28px]" />
      </button>

      {/* Dropdown Menu */}
      {isOpen && (
        <div className="absolute right-0 mt-2 w-96 bg-white border border-gray-200 rounded-lg shadow-lg z-50">
          <div className="p-4">
            <ul className="flex justify-between items-center border-b pb-2">
              <li className="font-semibold text-[24px] text-black">Shopping Cart</li>
              <li>
                <Image src={lock} alt="lock" />
              </li>
            </ul>

            {/* Check if cart is empty */}
            {cart.length === 0 ? (
              <p className="text-center text-gray-500 py-4">Your cart is empty.</p>
            ) : (
              <>
                {/* Cart Items */}
                <div className="max-h-60 overflow-y-auto">
                  {cart.map((item) => (
                    <div key={item.id} className="flex items-center justify-between mt-4">
                      <div className="flex items-center gap-4">
                        <Image
                          src={item.imagePath}
                          alt={item.name}
                          width={50}
                          height={50}
                          className="rounded bg-[#FBEBB5] w-[105px] h-[105px]"
                        />
                        <div>
                          <p className="font-normal text-black">{item.name}</p>
                          <ul className="flex gap-2 pt-3">
                            <li className="flex text-sm text-black font-light">{item.quantity}</li>
                            <li className="flex text-sm text-black font-light">x</li>
                            <li className="text-[#B88E2F]">Rs. {item.price.toLocaleString()}</li>
                            <li className="cursor-pointer ml-6">
                              <Image
                                src={cross}
                                alt="remove"
                                onClick={() => removeFromCart(item.id)}
                              />
                            </li>
                          </ul>
                        </div>
                      </div>
                    </div>
                  ))}
                </div>

                {/* Total Price */}
                <div className="flex justify-between items-center mt-4 pt-2 border-t">
                  <p className="text-black text-sm">Subtotal</p>
                  <p className="text-[#B88E2F] font-semibold">
                    Rs. {subtotal.toLocaleString()}
                  </p>
                </div>

                {/* Buttons */}
                <div className="flex justify-between items-center mt-4">
                  <Link href="/cart">
                    <button className="px-6 mt-4 w-full border border-black text-black py-2 rounded-full hover:bg-blue-300 transition duration-300 ml-6">
                      View Cart
                    </button>
                  </Link>

                  <Link href="/checkout">
                    <button className="px-6 mt-4 w-full border border-black text-black py-2 rounded-full hover:bg-blue-300 transition duration-300 mr-6">
                      Checkout
                    </button>
                  </Link>
                </div>
              </>
            )}
          </div>
        </div>
      )}
    </div>
  );
}
```

#### 10-checkout/page.tsx

```typescript
"use client";

import Foot from "../foot/page";
import FormDetails from "./formDetail";
import CheckoutHeader from "./checkoutHeader";

export default function Checkout() {

    return (
        <div>
            {/* checkout Header */}
            <CheckoutHeader />
            {/*  Main Wrapper (Left & Right Sections) */}
      <div className="container mx-auto px-4 sm:px-6 lg:px-12 xl:px-20 mt-12 flex flex-col lg:flex-row gap-12">
                {/* Left side: Billing Details */}
                <FormDetails />
            </div>
            
                <div>
                <Foot />
                </div>
                </div>
            )}
```

#### 11-FormDetails.tsx

```typescript
"use client"
import React from 'react';
import { SubmitHandler, useForm } from 'react-hook-form';
import ProductSummary from './productSummary';
import Image from "next/image";
import dot from "../../../public/assets/checkout/Ellipse1.png";
import Link from 'next/link';

type FormData = {
  firstName: string;
  lastName: string;
  email: string;
  phone: number;
  companyName?: string;
  country: string;
  streetAddress: string;
  city: string;
  province: string;
  ZIPcode: string;
  AdditionalInformation:string,
  paymentOption: string;
};

export default function FormDetails() {

  const {
    register,
    handleSubmit,
    formState: { errors },
    reset,
  } = useForm<FormData>();

  const onSubmit: SubmitHandler<FormData> = (data:any) => {
    console.log("Form Submitted Successfully:", data);
    alert("🎉 Your order has been placed successfully!");
    reset(); // Reset the form after successful submission
  };


  return (
    <section className='gap-2 font-Poppins col-span-6 ml-10 flex-col'>
      <form onSubmit={handleSubmit(onSubmit)} className="space-y-6">
        <div className="flex flex-wrap mt-12 ml-10 lg:flex-nowrap">
          <div className='-mr-36 flex'>
            <div className=''>
              <div className='font-semibold text-[36px]'>
                Billing details
              </div>
              <br />
              <div className='flex flex-row'>
                <div className='flex flex-col'>
                  <label>First Name</label>
                  <br />
                  <input type="text"
                    placeholder="First name"
                    {...register("firstName", { required: "First name is required" })}

                    className='border border-black py-3 pl-2 w-3/4 max-w-sm rounded-md focus:ring-2 focus:border-black' />
               {errors.firstName && (
                    <p className="text-red-500 text-sm">{errors.firstName.message}</p>
                  )}

                </div>

                <div className='flex flex-col'>
                  <label>Last Name</label>
                  <br />
                  <input type="text"
                    placeholder="Last name"
                    {...register("lastName", { required: "Last name is required" })}
           className='border border-black py-3 pl-2 w-3/4 max-w-sm rounded-md focus:ring-2 focus:border-black' />
           {errors.lastName && (
                    <p className="text-red-500 text-sm">{errors.lastName.message}</p>
                  )}

                </div>
              </div>
              <br />
              <br />
              <label>Company Name (Optional)</label>
              <br />
              <br />
              <input type="tel"
                placeholder="Company Name (Optional)"
                               {...register("companyName")}
                className='border border-black p-4 w-full max-w-sm rounded-md focus:ring-2 focus:border-black' />
              <br />
              <br />
              <label>Country / Region</label>
              <br />
              <br />
              <select {...register("country", { required: "Country is required" })}
                className='border border-black p-4 w-full max-w-sm rounded-md focus:ring-2 focus:border-black'>
                <option value="Sri Lanka">Sri Lanka</option>
                <option value="Pakistan">Pakistan</option>
                <option value="India">India</option>
              </select>
              {errors.country && (
                <p className="text-red-500 text-sm">{errors.country.message}</p>
              )}
              <br />
              <br />
              <label>Street Address</label>
              <br />
              <br />
              <input type="text"
                placeholder="Street address"
                {...register("streetAddress", { required: "Street address is required" })}
                className='border border-black p-4 w-full max-w-sm rounded-md focus:ring-2 focus:border-black' />
                            {errors.streetAddress && (
                <p className="text-red-500 text-sm">{errors.streetAddress.message}</p>
              )}

              <br />
              <br />
              <label>Town / City</label>
              <br />
              <br />
              <input type="text"
                placeholder="Town / City"
                {...register("city", {})}
                className='border border-black p-4 w-full max-w-sm rounded-md focus:ring-2 focus:border-black' />
              <br />
              <br />
              <label>Province</label>
              <br />
              <br />
              <select {...register("province")}
                className='border border-black p-4 w-full max-w-sm rounded-md focus:ring-2 focus:border-black'>
                <option value="Western Province">Western Province</option>
                <option value="Eastern Province">Eastern Province</option>
                <option value="Southern Province">Southern Province</option>
              </select>
              <br />
              <br />
              <label>ZIP code</label>
              <br />
              <br />
              <input type="tel"
                placeholder="ZIP code"
                {...register("ZIPcode", { maxLength: 12 })}
                className='border border-black p-4 w-full max-w-sm rounded-md focus:ring-2 focus:border-black' />
              <br />
              <br />
              <label>Phone</label>
              <br />
              <br />
              <input type="tel"
                placeholder="Phone"
                {...register("phone", { required: "Phone number is required", maxLength: 12 })}
               className='border border-black p-4 w-full max-w-sm rounded-md focus:ring-2 focus:border-black' />
              {errors.phone && <p className="text-red-500 text-sm">{errors.phone.message}</p>}

              <br />
              <br />
              <label>Email</label>
              <br />
              <br />
              <input type="text"
                placeholder="Email"
                {...register("email", {required: "Email is required", pattern: {value: /^\S+@\S+$/i,
                    message: "Invalid email format",  },})}
             className='border border-black p-4 w-full max-w-sm rounded-md focus:ring-2 focus:border-black' />
             {errors.email && <p className="text-red-500 text-sm">{errors.email.message}</p>}
              <br />
              <br />
              <input type="tel"
                placeholder="Additional information"
                {...register("AdditionalInformation", { required: false })}
                className='border border-black p-4 w-full max-w-sm rounded-md focus:ring-2 focus:border-black' />
            </div>
            {/* Right side: Product Summary */}
            <div>
              <ProductSummary />
              <br />
              <br />
              {/*  Order payment method*/}
              <div className="mt-6 ml-52">
                <ul className="border-t flex flex-col lg:flex-row mt-4 gap-4 lg:gap-2 lg:-ml-40">
                  <li><Image src={dot} alt="dot" className="mt-2 lg:mt-5" /></li>
                  <li className="mt-1 lg:mt-4">Direct Bank Transfer</li>
                </ul>
                <p className="font-light text-justify text-[#9F9F9F] lg:-ml-40 mt-2">Make your payment directly into our bank account. Please use<br />
                  your Order ID as the payment reference. Your order will not be<br />
                  shipped until the funds have cleared in our account.</p>
                <div className="flex flex-col space-y-4 lg:-ml-40">

                  {/* Option 1: Direct Bank Transfer */}
                  
                  <div className="flex flex-col gap-4 mt-4 font-poppins font-light text-[#9F9F9F]">
                <label>
                  <input
                    type="radio"
                    value="Direct Bank Transfer"
                    {...register("paymentOption", { required: "Select a payment option" })}
                    className="mr-2"
                  />
                  Direct Bank Transfer
                </label>
                <label>
                  <input
                    type="radio"
                    value="Cash On Delivery"
                    {...register("paymentOption", { required: "Select a payment option" })}
                    className="mr-2"
                  />
                  Cash On Delivery
                </label>
              </div>
              {errors.paymentOption && (
                <p className="text-red-500 text-sm">{errors.paymentOption.message}</p>
              )}                  <p className="font-light text-[#000000] mt-2">Your personal data will be used to support your experience<br />
                    throughout this website, to manage access to your account, and<br />
                    for other purposes described in our<span className='font-semibold'><Link href="/shop"> privacy policy</Link></span>.</p>
                </div>
              </div>
              <br />
              <br />
              {/* Place Order Button */}
              <div className="flex justify-center">
                <button
                  type="submit"
                  className="my-6 py-3 mt-10 !w-[318px] sm:w-auto text-black font-normal rounded-2xl hover:bg-blue-400 outline-[1px] outline outline-black"
                >
                  Place order
                </button>
              </div>
            </div>
          </div>
        </div>
      </form>
    </section>
  );
}
```

#### 12-ProductSummary.tsx

```typescript
"use client"
import { useCart } from "../contexts/cartContext";

export default function ProductSummary(){
    const { state } = useCart();
    const { cart } = state;

    // Total and Subtotal Calculation
    const subtotal = cart.reduce(
        (total, item) => total + item.price * item.quantity, 0
    );
    const total = subtotal; // Adjust if taxes or additional fees are added


    return(
   <div>
        {/* Right side: Product Summary */}

                            <div className="flex flex-col w-full ml-56 lg:w-1/3 lg:pl-8 mt-12 lg:mt-0">
                    <div className="lg:w-1/3 w-full lg:pl-8 mt-12 lg:mt-0">
                        {/* Product Summary */}
                        <div className="flex flex-col lg:flex-row gap-8 lg:gap-40">

                            {/* Left Column: Product Names and Info */}

                            <ul className="lg:-ml-40">
                                <li className="font-medium text-[24px] leading-[36px]">Product</li>
                                {cart.map((item) => (
                                    <li
                                        key={item.id}
                                        className="font-normal text-[16px] leading-[24px] mt-8 mb-16"
                                    >
                                        {item.name} x {item.quantity}
                                    </li>
                                ))}
                                <li className="font-normal text-[16px] leading-[24px] mt-8 mb-16">Subtotal</li>
                                <li className="font-normal text-[16px] leading-[24px] mt-8 mb-16">Total</li>
                            </ul>

                            {/* Right Column: Prices */}
                            <ul>
                                <li className="font-medium text-[24px] leading-[36px]">Subtotal</li>
                                {cart.map((item) => (
                                    <li
                                        key={item.id}
                                        className="font-normal text-[16px] leading-[24px] mt-8 mb-16"
                                    >
                                        Rs. {(item.price * item.quantity).toLocaleString()} {/* Price x Quantity */}
                                    </li>
                                ))}
                                <li className="font-normal text-[16px] leading-[24px] mt-8 mb-16">
                                    Rs. {subtotal.toLocaleString()} {/* Subtotal */}
                                </li>
                                <li className="font-bold text-[24px] leading-[36px] text-[#B88E2F] mt-8 mb-16">
                                    Rs. {subtotal.toLocaleString()} {/* Total */}
                                </li>
                            </ul>
                        </div>
                    </div>
                    </div>
        </div>
    )
}
```

#### 13-CheckoutHeader.tsx

```typescript
import React from "react";
import Link from "next/link";
import Image from "next/image";
import shop from "../../../public/assets/shopimages/shop1pic.png";
import logo from "../../../public/assets/checkout/checkoutlogo.png";
import small from "../../../public/assets/shopimages/shopSlogo.png";

const CheckoutHeader = () => {
  return (
    <div className="relative bg-white w-full pt-10">
      <Image src={shop} alt="shop" className="h-[316px] w-full" />
      <ul className="absolute top-1/2 left-1/2 transform -translate-x-1/2 -translate-y-1/2">
        <li>
          <Image src={logo} alt="logo" />
        </li>
        <li className="flex font-poppins pb-20 ml-14">
          <Link href="/">
            <span className="font-medium text-[16px] leading-[24px]">Home</span>
          </Link>
          <Image src={small} alt="small" className="w-[14px] h-[8px] m-2" />
          <Link href="/Shop">
            <span className="font-light text-[16px] leading-[24px]">Shop</span>
          </Link>
        </li>
      </ul>
    </div>
  );
};

export default CheckoutHeader;
```

## 3. Documentation
### 3.1 Steps Taken to Build and Integrate Components
#### 1-Setup Next.js and Sanity CMS

- Installed dependencies: next, sanity, @sanity/client
- Configured Sanity client to fetch product data
- 
#### 2-Built Dynamic Components

- [shop]/page.tsx → Displays all products dynamically.
- [id]/page.tsx → Reusable component for each product.
- ProductDetailClient.tsx → Each product detail component.
- RelatedProducts.tsx → Display same category product under product detail page.
- FilterBar.tsx → Display filter option for how to display products and how many products.
- Pagination.tsx → Divides large sets of data into smaller pages, allowing users to navigate through them efficiently.
- CartContext.tsx →  Manages the global shopping cart state using React Context, allowing components to add, remove, and update cart items.
- CartProvider.tsx → For CartProviderWrapper.
- CartDropdown.tsx → Display add to cart product in dropdown of cart icon.
- checkout → Divide checkout component into 3 small pages and combine in page.tsx.  
      - FormDetails.tsx → Make Form detail page with react form hook for client details.
      - ProductSummary.tsx → To sum up price of products at the time of checkout which was selected in cart.
      - CheckoutHeader.tsx → Display checkout header.

#### 3-Implemented Dynamic Routing

    - Used Next.js App Router (app/Shop/[id]/page.tsx) for product detail pages.
    - Fetched data from Sanity CMS dynamically.

### 3.2 Challenges Faced and Solutions Implemented
      
| **Challenge**                             | **Solution**                                    |
|-------------------------------------------|-------------------------------------------------|
| Empty Image Error in Next.js              | Use `product.imagePath`                         |
|  Error on Data fetching                   | Use   `` for query                              |
| Duplicate Products in Sanity              | Use condition to fix                            |

### 3.3 Best Practices Followed

- ✅ Code Reusability → Created separate ProductCard, ProductList, and SearchBar components.
- ✅ Performance Optimization → Used server-side fetching (getStaticProps) where needed.
- ✅ Error Handling → Handled missing data with default values ("No description available").
- ✅ SEO Optimization → Used generateMetadata() in Next.js 15 for dynamic SEO metadata.





