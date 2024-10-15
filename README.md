### 📁 Project Folder and File Structure

```
📁 NODE-...
├── 📁 middlewares
│   ├── 📄 checkRestaurant.ts
│   ├── 📄 errorHandler.ts
│   └── 📄 validate.ts
│
├── 📁 node_modules
│
├── 📁 routes
│   ├── 📄 cuisines.ts
│   └── 📄 restaurants.ts
│
├── 📁 schemas
│   ├── 📄 restaurant.ts
│   └── 📄 review.ts
│
├── 📁 seed
│   ├── 📄 bloomFilter.ts
│   └── 📄 createIndex.ts
│
├── 📁 utils
│   ├── 📄 client.ts
│   ├── 📄 keys.ts
│   └── 📄 responses.ts
│
├── 📄 .env
├── 📄 .gitignore
├── 📄 index.ts
├── 📄 LICENSE
├── 📄 package.json
└── 📄 pnpm-lock.yaml

```

---

### ⚙️ Initial Setup: Install Packages and Configure `tsconfig.json`

Below is your `tsconfig.json` configuration:

```json
{
  "compilerOptions": {
    "esModuleInterop": true,
    "skipLibCheck": true,
    "target": "es2022",
    "allowJs": true,
    "resolveJsonModule": true,
    "moduleDetection": "force",
    "isolatedModules": true,
    "verbatimModuleSyntax": true,
    "strict": true,
    "noUncheckedIndexedAccess": true,
    "noImplicitOverride": true,
    "module": "NodeNext",
    "outDir": "dist",
    "sourceMap": true,
    "lib": ["es2022"]
  }
}

```

---

### 🚀 Setting Up Express: `index.ts` File

The `index.ts` file initializes the Express application:

```tsx
import express from "express";
// import restaurantsRouter from "./routes/restaurants.js";
// import cuisinesRouter from "./routes/cuisines.js";
// import { errorHandler } from "./middlewares/errorHandler.js";

const PORT = process.env.PORT || 3000;
const app = express();

app.use(express.json());
// app.use("/restaurants", restaurantsRouter);
// app.use("/cuisines", cuisinesRouter);

// app.use(errorHandler);

app
  .listen(PORT, () => {
    console.log(`Application running on port ${PORT}`);
  })
  .on("error", (error) => {
    throw new Error(error.message);
  });

```

- **Key Features**:
    - ✅ Initializes Express and listens on the specified port.
    - ✅ Error handling during server startup (`on("error")`).

---

### 🔧 Utility Functions: `responses.ts`

This file defines helper functions for consistent responses:

```tsx
import type { Response } from "express";

/**
 * Sends a successful HTTP response.
 * @param {Response} res - Express response object
 * @param {any} data - Response data
 * @param {string} message - Success message (default: 'Success')
 */
export function successResponse(
  res: Response,
  data: any,
  message: string = "Success"
) {
  return res.status(200).json({ success: true, message, data });
}

/**
 * Sends an error HTTP response.
 * @param {Response} res - Express response object
 * @param {number} status - HTTP status code
 * @param {string} error - Error message
 */
export function errorResponse(res: Response, status: number, error: string) {
  return res.status(status).json({ success: false, error });
}

```

- **`successResponse`**:
    
    ✅ Returns a successful response (`200 OK`) with a message and data.
    
- **`errorResponse`**:
    
    ⚠️ Returns an error response with the specified HTTP status and error message.
    

---

### 🛠️ Error Handling Middleware: `errorHandler.ts`

This middleware globally handles errors in the application:

```tsx
import type { Request, Response, NextFunction } from "express";
import { errorResponse } from "../utils/responses.js";

/**
 * Express error handling middleware.
 * @param {any} err - Error object
 * @param {Request} req - Express request object
 * @param {Response} res - Express response object
 * @param {NextFunction} next - Express next function
 */
export function errorHandler(
  err: any,
  req: Request,
  res: Response,
  next: NextFunction
) {
  console.error(err);
  errorResponse(res, 500, err);
}

```

- **Key Features**:
    - ⚠️ Captures and logs errors globally.
    - 🛑 Sends a `500 Internal Server Error` response using the `errorResponse` utility function.

### 🚦 Route Initialization: Creating Express Routers

1. **Create an Express Router** for your routes:
    
    ```tsx
    import express from "express";
    const router = express.Router();
    
    export default router;
    
    ```
    
2. **Define a Basic Route** with a GET request:
    
    ```tsx
    import express from "express";
    const router = express.Router();
    
    router.get("/", async (req, res) => {
      res.send("Hello world");
    });
    
    export default router;
    
    ```
    

---

### 🛤️ Initialize Routes in the `routes` Folder

To keep your project organized, initialize these two routers within the `routes` folder as shown below:

```
📁 routes
├── 📄 route1.ts
├── 📄 route2.ts

```

Each route file will follow the above structure, ready to handle requests for specific paths in your Express application.

### 📝 Zod Validation Schema: Setting Up Inside the `schemas` Folder

In this section, we will define validation schemas using **Zod**, a TypeScript-first schema declaration and validation library. These schemas will help ensure that the data for restaurants, their details, and their reviews conform to the expected structure before being processed by your application.

### 1. Create the Restaurant Schema

We begin by creating a schema for a restaurant, which validates the basic information required:

```tsx
import { z } from "zod";

export const RestaurantSchema = z.object({
  name: z.string().min(1), // The name of the restaurant must be a non-empty string
  location: z.string().min(1), // The location of the restaurant must be a non-empty string
  cuisines: z.array(z.string().min(1)), // An array of cuisines offered by the restaurant, each must be a non-empty string
});

```

### 2. Create the Restaurant Details Schema

Next, we define a schema for more detailed information about a restaurant, including links and contact information:

```tsx
export const RestaurantDetailsSchema = z.object({
  links: z.array(
    z.object({
      name: z.string().min(1), // The name of the link must be a non-empty string
      url: z.string().min(1), // The URL associated with the link must be a non-empty string
    })
  ),
  contact: z.object({
    phone: z.string().min(1), // The restaurant's contact phone number must be a non-empty string
    email: z.string().email(), // The restaurant's contact email must be a valid email format
  }),
});

```

### 3. Create the Review Schema

To validate user-generated reviews for restaurants, we will define a schema specifically for reviews:

```tsx
export const ReviewSchema = z.object({
  review: z.string().min(1), // The review text must be a non-empty string
  rating: z.number().min(1).max(5), // The rating must be a number between 1 and 5
});

```

### 4. Infer Types from Schemas

To facilitate TypeScript type checking, we infer types based on the defined schemas:

```tsx
export type Restaurant = z.infer<typeof RestaurantSchema>;
export type RestaurantDetails = z.infer<typeof RestaurantDetailsSchema>;
export type Review = z.infer<typeof ReviewSchema>; // Type for review

```

- **Type Definitions**:
    - `Restaurant`: This type represents the structure of a restaurant as defined by the `RestaurantSchema`.
    - `RestaurantDetails`: This type represents the structure of the detailed restaurant information as defined by the `RestaurantDetailsSchema`.
    - `Review`: This type represents the structure of a restaurant review as defined by the `ReviewSchema`.

---

### 📂 File Structure for Schemas

After implementing these schemas, your `schemas` folder should include the following structure:

```
📁 schemas
├── 📄 restaurant.ts   // Contains RestaurantSchema, RestaurantDetailsSchema, and ReviewSchema
└── 📄 review.ts       // Additional schema for reviews (if applicable)

```

---

### 🛡️ Middleware for Validation: `validate.ts` in the `middlewares` Folder

### 1. Purpose

- The `validate.ts` middleware function validates incoming request bodies against specified Zod schemas.

### 2. Code Implementation

```tsx
import type { Request, Response, NextFunction } from "express";
import { ZodSchema } from "zod";

export const validate =
  <T>(schema: ZodSchema<T>) =>
  (req: Request, res: Response, next: NextFunction) => {
    const result = schema.safeParse(req.body); // Validate request body against the schema
    if (!result.success) {
      // If validation fails, return a 400 response with error details
      return res
        .status(400)
        .json({ success: false, errors: result.error.errors });
    }
    next(); // If validation succeeds, proceed to the next middleware/handler
  };

```

### 3. Key Features

- **Generic Function**: Accepts a type parameter `<T>` to work with any Zod schema.
- **Schema Validation**: Utilizes `schema.safeParse(req.body)` for validation.
- **Error Handling**:
    - Responds with `400 Bad Request` and error details if validation fails.
    - Calls `next()` to continue processing if validation is successful.

---

### 📂 Example Route Setup Using Validation Middleware

In this section, we demonstrate how to use the `validate` middleware in a route for handling restaurant data.

```tsx
import express from "express";
import { validate } from "../middlewares/validate.js";
import { RestaurantSchema, type Restaurant } from "../schemas/restaurant.js";

const router = express.Router();

router.post("/", validate(RestaurantSchema), async (req, res) => {
  const data = req.body as Restaurant; // Type assertion to Restaurant
  res.send("Hello world"); // Respond with a message
});

export default router;

```

### Explanation:

- **Route Definition**:
    - The route listens for `POST` requests at the root path (`/`).
- **Middleware Integration**:
    - The `validate` middleware checks the request body against `RestaurantSchema` before processing.
- **Type Assertion**:
    - The request body is asserted to the `Restaurant` type for type safety.
- **Response**:
    - A simple response is sent back with the message "Hello world".

---

### 📂 File Structure for Middleware

```
📁 middlewares
├── 📄 checkRestaurant.ts   // Other middleware functions (if applicable)
├── 📄 errorHandler.ts      // Error handling middleware
├── 📄 validate.ts          // Validation middleware for schema validation

```

This structure promotes modularity and ensures robust validation of incoming requests in your application.

### 🛠️ Setting Up Redis Client: `client.ts` in the `utils` Folder

### 1. Purpose

- This utility file initializes a Redis client with proper error handling and connection logging.

### 2. Code Implementation

```tsx
import { createClient, type RedisClientType } from "redis";

let client: RedisClientType | null = null;

export async function initializeRedisClient() {
  if (!client) {
    client = createClient(); // Create a new Redis client
    client.on("error", (error) => {
      console.error(error); // Log any connection errors
    });
    client.on("connect", () => {
      console.log("Redis connected"); // Log successful connection
    });
    await client.connect(); // Establish the connection
  }
  return client; // Return the Redis client instance
}

```

### 3. Key Features

- **Singleton Pattern**: Ensures only one instance of the Redis client is created.
- **Error Handling**:
    - Listens for connection errors and logs them.
    - Logs a message when connected successfully.
- **Async Initialization**: The client connection is established asynchronously.

---

### 🗝️ Utility Function for Key Generation: `keys.ts`

### 1. Purpose

- This utility function generates a standardized key format for Redis entries.

### 2. Code Implementation

```tsx
export function getKeyName(...args: string[]) {
  return `bites:${args.join(":")}`; // Create a key by joining arguments with a colon
}

```

### 3. Key Features

- **Dynamic Key Creation**: Accepts multiple string arguments to generate keys.
- **Consistent Naming**: Prefixes keys with `bites:` for organization.

```jsx
const key = getKeyName("restaurant", "123");
// Output: "bites:restaurant:123"
```

---

### 🗄️ Initializing Redis Client in Components

To use the Redis client in any component, simply call the initialization function:

```tsx
const client = await initializeRedisClient(); // Initialize and retrieve the Redis client

```

### Explanation:

- **Async Initialization**: The `await` keyword ensures that the Redis client is fully connected before use.
- **Reuse**: This approach allows for easy reuse of the Redis client across your application.

---

### 🏷️ Generating Hash Keys for Restaurants

To efficiently manage restaurant data in Redis, we implement a function to create hash keys and middleware to check for the existence of restaurants. This modular approach ensures clean code organization and reusability.

---

### 1. **Creating the Hash Key Function: `keys.ts`**

**Purpose**: This function generates a unique hash key for each restaurant based on its ID.

```tsx
export const restaurantKeyById = (id: string) => getKeyName("restaurants", id);

```

- **Key Features**:
    - **Hash Key Format**: Generates keys like `bites:restaurants:<id>`.
    - **Modularity**: Easy to generate keys for any restaurant using its ID.

---

### 2. **Creating Middleware: `checkRestaurantExists.ts`**

**Purpose**: This middleware checks whether a restaurant exists in the Redis database before processing requests.

**Code Implementation**:

```tsx
import type { Request, Response, NextFunction } from "express";
import { initializeRedisClient } from "../utils/client.js";
import { restaurantKeyById } from "../utils/keys.js";
import { errorResponse } from "../utils/responses.js";

export const checkRestaurantExists = async (
  req: Request,
  res: Response,
  next: NextFunction
) => {
  const { restaurantId } = req.params; // Get restaurant ID from request parameters

  // Validate restaurant ID
  if (!restaurantId) {
    return errorResponse(res, 400, "Restaurant ID not found"); // Error if ID is missing
  }

  const client = await initializeRedisClient(); // Initialize Redis client
  const restaurantKey = restaurantKeyById(restaurantId); // Generate the restaurant key
  const exists = await client.exists(restaurantKey); // Check if restaurant exists

  // Handle non-existing restaurant
  if (!exists) {
    return errorResponse(res, 404, "Restaurant Not Found"); // Error if restaurant does not exist
  }

  next(); // Proceed to the next middleware or route handler
};

```

- **Key Features**:
    - **ID Validation**: Checks if the restaurant ID is provided.
    - **Error Handling**: Returns specific error responses for missing or non-existing restaurants.
    - **Redis Interaction**: Utilizes Redis client to check for existence efficiently.

---

### 3. **Using Middleware in Routes: `routes.ts`**

**Purpose**: Integrate the middleware into the restaurant routes to ensure proper request handling.

**Code Implementation**:

```tsx
import express, { type Request } from "express";
import { validate } from "../middlewares/validate.js";
import { RestaurantSchema, type Restaurant } from "../schemas/restaurant.js";
import { initializeRedisClient } from "../utils/client.js";
import { nanoid } from "nanoid";
import { restaurantKeyById } from "../utils/keys.js";
import { successResponse } from "../utils/responses.js";
import { checkRestaurantExists } from "../middlewares/checkRestaurantId.js";

const router = express.Router();

// POST route to add a new restaurant
router.post("/", validate(RestaurantSchema), async (req, res, next) => {
  const data = req.body as Restaurant; // Type assertion for incoming data
  try {
    const client = await initializeRedisClient(); // Initialize Redis client
    const id = nanoid(); // Generate a unique ID for the new restaurant
    const restaurantKey = restaurantKeyById(id); // Generate the hash key for the restaurant
    const hashData = { id, name: data.name, location: data.location }; // Prepare hash data

    const addResult = await client.hSet(restaurantKey, hashData); // Add restaurant to Redis
    console.log(`Added ${addResult} fields`); // Log the result
    return successResponse(res, hashData, "Added new restaurant"); // Send success response
  } catch (error) {
    next(error); // Pass errors to the error handling middleware
  }
});

// GET route to retrieve restaurant details
router.get(
  "/:restaurantId",
  checkRestaurantExists, // Middleware to check if restaurant exists
  async (req: Request<{ restaurantId: string }>, res, next) => {
    const { restaurantId } = req.params; // Extract restaurant ID from request
    try {
      const client = await initializeRedisClient(); // Initialize Redis client
      const restaurantKey = restaurantKeyById(restaurantId); // Generate the restaurant key

      // Use Promise.all for concurrent Redis operations
      const [viewCount, restaurant] = await Promise.all([
        client.hIncrBy(restaurantKey, "viewCount", 1), // Increment view count
        client.hGetAll(restaurantKey), // Get all restaurant data
      ]);

      return successResponse(res, restaurant); // Send success response with restaurant data
    } catch (error) {
      next(error); // Pass errors to the error handling middleware
    }
  }
);

export default router;

```

- **Key Features**:
    - **Adding Restaurants**: Uses the `POST` method to create a new restaurant entry.
    - **Fetching Restaurant Details**: Uses the `GET` method to retrieve restaurant data, including incrementing the view count.
    - **Modular Integration**: Middleware checks are seamlessly integrated into the routes for efficient error handling.

---

### 

### 📜 Explanation of the Restaurant and Review Routes Code

The following code implements functionality for managing restaurants and their associated reviews using Redis for storage. Below is a detailed pointwise breakdown of each part of the code:

---

### 1. **Importing Required Modules**

```tsx
import express, { type Request } from "express";
import { validate } from "../middlewares/validate.js";
import { RestaurantSchema, type Restaurant } from "../schemas/restaurant.js";
import { initializeRedisClient } from "../utils/client.js";
import { nanoid } from "nanoid";
import {
  restaurantKeyById,
  reviewDetailsKeyById,
  reviewKeyById,
} from "../utils/keys.js";
import { errorResponse, successResponse } from "../utils/responses.js";
import { checkRestaurantExists } from "../middlewares/checkRestaurantId.js";
import { ReviewSchema, type Review } from "../schemas/review.js";

```

- **Purpose**: Import necessary modules and schemas for handling restaurants and reviews.
- **Components**:
    - `express`: Framework for building the API.
    - `validate`: Middleware for validating incoming data.
    - Schemas for restaurants and reviews to ensure data integrity.
    - `initializeRedisClient`: Utility to manage Redis connections.
    - `nanoid`: Library to generate unique IDs.
    - Key generation functions for Redis.
    - Response utilities for consistent API responses.

---

### 2. **Router Initialization**

```tsx
const router = express.Router();

```

- **Purpose**: Create an instance of an Express router to define the API endpoints.

---

### 3. **Adding a New Restaurant: `POST /`**

```tsx
router.post("/", validate(RestaurantSchema), async (req, res, next) => {
  const data = req.body as Restaurant;
  try {
    const client = await initializeRedisClient();
    const id = nanoid();
    const restaurantKey = restaurantKeyById(id);
    const hashData = { id, name: data.name, location: data.location };
    const addResult = await client.hSet(restaurantKey, hashData);
    console.log(`Added ${addResult} fields`);
    return successResponse(res, hashData, "Added new restaurant");
  } catch (error) {
    next(error);
  }
});

```

- **Purpose**: Adds a new restaurant to the Redis database.
- **Steps**:
    1. Validate incoming request data against `RestaurantSchema`.
    2. Initialize Redis client.
    3. Generate a unique ID for the restaurant.
    4. Create a hash data object containing restaurant details.
    5. Store the restaurant data in Redis using the generated hash key.
    6. Log the number of fields added and send a success response.

---

### 4. **Adding a Review: `POST /:restaurantId/reviews`**

```tsx
router.post(
  "/:restaurantId/reviews",
  checkRestaurantExists,
  validate(ReviewSchema),
  async (req: Request<{ restaurantId: string }>, res, next) => {
    const { restaurantId } = req.params;
    const data = req.body as Review;
    try {
      const client = await initializeRedisClient();
      const reviewId = nanoid();
      const reviewKey = reviewKeyById(restaurantId);
      const reviewDetailsKey = reviewDetailsKeyById(reviewId);
      const reviewData = {
        id: reviewId,
        ...data,
        timestamp: Date.now(),
        restaurantId,
      };
      await Promise.all([
        client.lPush(reviewKey, reviewId),
        client.hSet(reviewDetailsKey, reviewData),
      ]);
      return successResponse(res, reviewData, "Review added");
    } catch (error) {
      next(error);
    }
  }
);

```

- **Purpose**: Allows users to add a review for a specific restaurant.
- **Steps**:
    1. Check if the restaurant exists using the `checkRestaurantExists` middleware.
    2. Validate the incoming review data against `ReviewSchema`.
    3. Initialize Redis client.
    4. Generate a unique review ID.
    5. Create keys for storing the review in Redis.
    6. Create review data including the ID, data, timestamp, and associated restaurant ID.
    7. Store the review ID in a list (for the restaurant) and the review details in a hash.
    8. Send a success response with the added review data.

---

### 5. **Retrieving Reviews: `GET /:restaurantId/reviews`**

```tsx
router.get(
  "/:restaurantId/reviews",
  checkRestaurantExists,
  async (req: Request<{ restaurantId: string }>, res, next) => {
    const { restaurantId } = req.params;
    const { page = 1, limit = 10 } = req.query;
    const start = (Number(page) - 1) * Number(limit);
    const end = start + Number(limit) - 1;

    try {
      const client = await initializeRedisClient();
      const reviewKey = reviewKeyById(restaurantId);
      const reviewIds = await client.lRange(reviewKey, start, end);
      const reviews = await Promise.all(
        reviewIds.map((id) => client.hGetAll(reviewDetailsKeyById(id)))
      );
      return successResponse(res, reviews);
    } catch (error) {
      next(error);
    }
  }
);

```

- **Purpose**: Retrieves a paginated list of reviews for a specific restaurant.
- **Steps**:
    1. Check if the restaurant exists.
    2. Extract pagination parameters (page and limit) from the request query.
    3. Initialize Redis client.
    4. Get the list of review IDs for the restaurant using the Redis list.
    5. Fetch detailed review data for each review ID using `hGetAll`.
    6. Send the list of reviews as a success response.

---

### 6. **Deleting a Review: `DELETE /:restaurantId/reviews/:reviewId`**

```tsx
router.delete(
  "/:restaurantId/reviews/:reviewId",
  checkRestaurantExists,
  async (
    req: Request<{ restaurantId: string; reviewId: string }>,
    res,
    next
  ) => {
    const { restaurantId, reviewId } = req.params;

    try {
      const client = await initializeRedisClient();
      const reviewKey = reviewKeyById(restaurantId);
      const reviewDetailsKey = reviewDetailsKeyById(reviewId);
      const [removeResult, deleteResult] = await Promise.all([
        client.lRem(reviewKey, 0, reviewId),
        client.del(reviewDetailsKey),
      ]);
      if (removeResult === 0 && deleteResult === 0) {
        return errorResponse(res, 404, "Review not found");
      }
      return successResponse(res, reviewId, "Review deleted");
    } catch (error) {
      next(error);
    }
  }
);

```

- **Purpose**: Deletes a specific review for a restaurant.
- **Steps**:
    1. Check if the restaurant exists.
    2. Initialize Redis client.
    3. Generate keys for the review and its details.
    4. Remove the review ID from the restaurant's review list.
    5. Delete the review details from Redis.
    6. Send an error response if the review was not found; otherwise, send a success response.

---

### 7. **Retrieving Restaurant Details: `GET /:restaurantId`**

```tsx
router.get(
  "/:restaurantId",
  checkRestaurantExists,
  async (req: Request<{ restaurantId: string }>, res, next) => {
    const { restaurantId } = req.params;
    try {
      const client = await initializeRedisClient();
      const restaurantKey = restaurantKeyById(restaurantId);
      const [viewCount, restaurant] = await Promise.all([
        client.hIncrBy(restaurantKey, "viewCount", 1),
        client.hGetAll(restaurantKey),
      ]);
      return successResponse(res, restaurant);
    } catch (error) {
      next(error);
    }
  }
);

```

- **Purpose**: Retrieves details of a specific restaurant.
- **Steps**:
    1. Check if the restaurant exists.
    2. Initialize Redis client.
    3. Increment the view count for the restaurant.
    4. Fetch all restaurant details.
    5. Send the restaurant details as a success response.

---

### 🌐 Summary of API Functionality

- **Endpoints**:
    - **Add Restaurant**: `POST /` - Create a new restaurant.
    - **Add Review**: `POST /:restaurantId/reviews` - Add a review for a restaurant.
    - **Get Reviews**: `GET /:restaurantId/reviews` - Retrieve paginated reviews for a restaurant.
    - **Delete Review**: `DELETE /:restaurantId/reviews/:reviewId` - Remove a specific review.
    - **Get Restaurant Details**: `GET /:restaurantId` - Fetch details of a specific restaurant.

This modular approach provides clear separation of concerns and enhances maintainability of the codebase while leveraging Redis for efficient data management.

### Explanation of the Code in `createIndex.ts` and `bloomFilters.ts`

### 1. **File 1: `createIndex.ts`**

This code sets up an index for searching and sorting restaurant data using Redis' `FT.CREATE` command (Redisearch feature). It ensures that the indexed fields (`id`, `name`, `avgStars`) can be efficiently queried or filtered.

---

### 🛠️ **Purpose**

To create a text and numeric index for restaurants in Redis, enabling fast searching and sorting based on restaurant `name` and `avgStars` (average rating).

### 🔍 **Breakdown of Code**

```tsx
import { SchemaFieldTypes } from "redis";
import { initializeRedisClient } from "../utils/client.js";
import { indexKey, getKeyName } from "../utils/keys.js";

async function createIndex() {
  const client = await initializeRedisClient();

```

- **Purpose**:
    - Imports `SchemaFieldTypes` from Redis, which helps define the types of fields in the index.
    - `initializeRedisClient`: Initializes the Redis connection.
    - `indexKey`: The key used to identify the index in Redis.
    - `getKeyName`: Generates names for Redis keys based on a pattern.

```tsx
  try {
    await client.ft.dropIndex(indexKey);
  } catch (err) {
    console.log("No existing index to delete");
  }

```

- **Purpose**:
    - Attempts to delete the existing index if it exists using `ft.dropIndex`.
    - If no index is found, it logs a message and continues without failing.

```tsx
  await client.ft.create(
    indexKey,
    {
      id: {
        type: SchemaFieldTypes.TEXT,
        AS: "id",
      },
      name: {
        type: SchemaFieldTypes.TEXT,
        AS: "name",
      },
      avgStars: {
        type: SchemaFieldTypes.NUMERIC,
        AS: "avgStars",
        SORTABLE: true,
      },
    },
    {
      ON: "HASH",
      PREFIX: getKeyName("restaurants"),
    }
  );

```

- **Purpose**:
    - Defines the structure of the index:
        - **id**: Stored as a `TEXT` field.
        - **name**: Also a `TEXT` field (can be queried using full-text search).
        - **avgStars**: Stored as a `NUMERIC` field, with `SORTABLE: true`, which means it can be sorted based on the numeric value.
    - The index works on **hash** data types, specifically for keys with the prefix `restaurants`, generated by `getKeyName("restaurants")`.

```tsx
await createIndex();
process.exit();

```

- **Purpose**:
    - Executes the `createIndex` function to initialize the index.
    - Exits the process after execution.

```jsx
// createIndex.ts
// Importing the necessary types and functions
import { SchemaFieldTypes } from "redis"; // SchemaFieldTypes defines the field types in Redisearch.
import { initializeRedisClient } from "../utils/client.js"; // Initializes Redis client connection.
import { indexKey, getKeyName } from "../utils/keys.js"; // Key functions to generate Redis key names.

async function createIndex() {
  const client = await initializeRedisClient(); // Connect to Redis client.

  try {
    // Attempting to delete an existing index if it already exists.
    await client.ft.dropIndex(indexKey); 
  } catch (err) {
    // If there's no existing index, catch the error and log a message.
    console.log("No existing index to delete");
  }

  // Creating a new index with specific field types for Redisearch.
  await client.ft.create(
    indexKey, // Name of the index.
    {
      // Defining the fields for the index.
      id: {
        type: SchemaFieldTypes.TEXT, // 'id' is treated as a text field.
        AS: "id", // Alias for 'id' field.
      },
      name: {
        type: SchemaFieldTypes.TEXT, // 'name' is treated as a text field for full-text search.
        AS: "name", // Alias for 'name' field.
      },
      avgStars: {
        type: SchemaFieldTypes.NUMERIC, // 'avgStars' is a numeric field for storing ratings.
        AS: "avgStars", // Alias for 'avgStars' field.
        SORTABLE: true, // Allows sorting results based on 'avgStars'.
      },
    },
    {
      ON: "HASH", // Operates on hash data types in Redis.
      PREFIX: getKeyName("restaurants"), // Uses key names prefixed with "restaurants".
    }
  );
}

await createIndex(); // Execute the function to create the index.
process.exit(); // Exit the process after the index is created.

```

---

### 2. **File 2: `bloomFilters.ts`**

This code creates a Bloom filter in Redis to check whether a value (such as an ID or name) exists in a probabilistic manner with a very low false-positive rate. Bloom filters are efficient in memory usage but don't support deletion.

---

### 🛠️ **Purpose**

To create a Redis Bloom filter for tracking unique restaurant names or IDs and quickly checking if a name or ID has already been added, with a high degree of certainty.

### 🔍 **Breakdown of Code**

```tsx
import { initializeRedisClient } from "../utils/client.js";
import { bloomKey } from "../utils/keys.js";

async function createBloomFilter() {
  const client = await initializeRedisClient();

```

- **Purpose**:
    - Imports necessary utilities.
    - `initializeRedisClient`: Sets up the Redis client connection.
    - `bloomKey`: The key under which the Bloom filter will be stored.

```tsx
  await Promise.all([
    client.del(bloomKey),
    client.bf.reserve(bloomKey, 0.0001, 1000000),
  ]);

```

- **Purpose**:
    - Deletes any existing Bloom filter at `bloomKey`.
    - Reserves a new Bloom filter:
        - **0.0001**: False-positive rate (0.01%), meaning a small chance that a non-existent element could be reported as existing.
        - **1,000,000**: The expected number of unique items the Bloom filter can hold.

```tsx
await createBloomFilter();
process.exit();

```

- **Purpose**:
    - Executes the `createBloomFilter` function.
    - Exits the process once the filter is created.

---

### 📝 **Summary**

1. **`createIndex.ts`**:
    - **Purpose**: Create a searchable index for restaurants using Redisearch.
    - **Steps**:
        1. Deletes an existing index (if any).
        2. Creates a new index with fields `id`, `name`, and `avgStars` (sortable).
        3. Operates on Redis hashes with the prefix `restaurants`.
2. **`bloomFilters.ts`**:
    - **Purpose**: Create a Bloom filter to quickly check for the existence of items (such as restaurant IDs or names).
    - **Steps**:
        1. Deletes an existing Bloom filter (if any).
        2. Reserves a new Bloom filter with a 0.01% false-positive rate and capacity for 1 million items.

```jsx
// Importing the Redis client and key generation functions.
import { initializeRedisClient } from "../utils/client.js"; // Initializes Redis client connection.
import { bloomKey } from "../utils/keys.js"; // Key function to generate the Redis Bloom filter key.

async function createBloomFilter() {
  const client = await initializeRedisClient(); // Connect to Redis client.

  await Promise.all([
    // Deletes the existing Bloom filter if it exists, to start fresh.
    client.del(bloomKey),
    
    // Reserves a new Bloom filter with a specified false-positive rate and capacity.
    client.bf.reserve(bloomKey, 0.0001, 1000000), 
    // 0.0001: False-positive rate (0.01%), meaning a small chance that a non-existent element could be reported as existing.
    // 1000000: The expected number of unique items the filter will handle.
  ]);
}

await createBloomFilter(); // Execute the function to create the Bloom filter.
process.exit(); // Exit the process after the Bloom filter is created.

```

### Here is the complete code of resturant controller

```jsx
// Importing necessary modules and types for the Express router.
import express, { type Request } from "express"; 
import { validate } from "../middlewares/validate.js"; // Middleware for schema validation.
import { RestaurantDetailsSchema, RestaurantSchema, type Restaurant, type RestaurantDetails } from "../schemas/restaurant.js"; // Schemas for restaurant and restaurant details.
import { initializeRedisClient } from "../utils/client.js"; // Utility function to initialize Redis client.
import { nanoid } from "nanoid"; // Generates unique IDs for restaurants, reviews, etc.
import { 
  bloomKey, 
  cuisineKey, 
  cuisinesKey, 
  indexKey, 
  restaurantCuisinesKeyById, 
  restaurantDetailsKeyById, 
  restaurantKeyById, 
  restaurantsByRatingKey, 
  reviewDetailsKeyById, 
  reviewKeyById, 
  weatherKeyById 
} from "../utils/keys.js"; // Utility functions to generate Redis keys.
import { errorResponse, successResponse } from "../utils/responses.js"; // Helper functions for standard API responses.
import { checkRestaurantExists } from "../middlewares/checkRestaurantId.js"; // Middleware to check if a restaurant exists.
import { ReviewSchema, type Review } from "../schemas/review.js"; // Schema for reviews.

const router = express.Router(); // Initializing the Express router.

// GET route for listing restaurants sorted by rating (supports pagination).
router.get("/", async (req, res, next) => {
  const { page = 1, limit = 10 } = req.query; // Extract pagination parameters from the request.
  const start = (Number(page) - 1) * Number(limit); // Calculate the start index for pagination.
  const end = start + Number(limit); // Calculate the end index.

  try {
    const client = await initializeRedisClient(); // Initialize Redis client.
    const restaurantIds = await client.zRange(restaurantsByRatingKey, start, end, { REV: true }); // Fetch restaurant IDs sorted by rating in reverse order.
    
    // Fetch restaurant details for each restaurant ID.
    const restaurants = await Promise.all(
      restaurantIds.map((id) => client.hGetAll(restaurantKeyById(id)))
    );
    
    return successResponse(res, restaurants); // Return the list of restaurants as a successful response.
  } catch (error) {
    next(error); // Handle errors by passing to the error middleware.
  }
});

// POST route to add a new restaurant.
router.post("/", validate(RestaurantSchema), async (req, res, next) => {
  const data = req.body as Restaurant; // Extract the restaurant data from the request body.
  
  try {
    const client = await initializeRedisClient(); // Initialize Redis client.
    const id = nanoid(); // Generate a unique ID for the restaurant.
    const restaurantKey = restaurantKeyById(id); // Generate the Redis key for the restaurant.

    const bloomString = `${data.name}:${data.location}`; // Create a unique identifier for the Bloom filter (based on name and location).
    const seenBefore = await client.bf.exists(bloomKey, bloomString); // Check if the restaurant already exists using the Bloom filter.
    
    if (seenBefore) {
      return errorResponse(res, 409, "Restaurant already exists"); // If the restaurant is found in the Bloom filter, return an error.
    }
    
    // Prepare the hash data to be stored in Redis for the restaurant.
    const hashData = { id, name: data.name, location: data.location };
    
    // Perform all operations in parallel:
    await Promise.all([
      // Add the restaurant's cuisines to various sets in Redis.
      ...data.cuisines.map((cuisine) =>
        Promise.all([
          client.sAdd(cuisinesKey, cuisine), // Add the cuisine to the global list of cuisines.
          client.sAdd(cuisineKey(cuisine), id), // Associate the restaurant ID with the cuisine.
          client.sAdd(restaurantCuisinesKeyById(id), cuisine), // Associate the cuisine with the restaurant.
        ])
      ),
      client.hSet(restaurantKey, hashData), // Store the restaurant's details in a hash.
      client.zAdd(restaurantsByRatingKey, { score: 0, value: id }), // Add the restaurant to the sorted set by rating (initial rating is 0).
      client.bf.add(bloomKey, bloomString), // Add the restaurant to the Bloom filter.
    ]);

    return successResponse(res, hashData, "Added new restaurant"); // Return success response after the restaurant is added.
  } catch (error) {
    next(error); // Handle errors by passing to the error middleware.
  }
});

// GET route for searching restaurants by name.
router.get("/search", async (req, res, next) => {
  const { q } = req.query; // Extract the search query from the request.
  
  try {
    const client = await initializeRedisClient(); // Initialize Redis client.
    const results = await client.ft.search(indexKey, `@name:${q}`); // Perform a full-text search on the name field in the Redisearch index.
    
    return successResponse(res, results); // Return the search results.
  } catch (error) {
    next(error); // Handle errors by passing to the error middleware.
  }
});

// POST route to add detailed information for a specific restaurant.
router.post(
  "/:restaurantId/details", 
  checkRestaurantExists, // Middleware to check if the restaurant exists.
  validate(RestaurantDetailsSchema), // Validate the request body against the RestaurantDetailsSchema.
  async (req: Request<{ restaurantId: string }>, res, next) => {
    const { restaurantId } = req.params; // Extract the restaurant ID from the URL parameters.
    const data = req.body as RestaurantDetails; // Extract the restaurant details from the request body.

    try {
      const client = await initializeRedisClient(); // Initialize Redis client.
      const restaurantDetailsKey = restaurantDetailsKeyById(restaurantId); // Generate the Redis key for the restaurant details.
      
      // Set the restaurant details as a JSON object in Redis.
      await client.json.set(restaurantDetailsKey, ".", data);
      
      return successResponse(res, {}, "Restaurant details added"); // Return success response.
    } catch (error) {
      next(error); // Handle errors by passing to the error middleware.
    }
  }
);

// GET route to fetch the detailed information for a specific restaurant.
router.get(
  "/:restaurantId/details", 
  checkRestaurantExists, // Middleware to check if the restaurant exists.
  async (req: Request<{ restaurantId: string }>, res, next) => {
    const { restaurantId } = req.params; // Extract the restaurant ID from the URL parameters.

    try {
      const client = await initializeRedisClient(); // Initialize Redis client.
      const restaurantDetailsKey = restaurantDetailsKeyById(restaurantId); // Generate the Redis key for the restaurant details.
      
      // Retrieve the restaurant details from Redis.
      const details = await client.json.get(restaurantDetailsKey);
      
      return successResponse(res, details); // Return the details as a success response.
    } catch (error) {
      next(error); // Handle errors by passing to the error middleware.
    }
  }
);

// GET route to fetch weather data for a specific restaurant based on location.
router.get(
  "/:restaurantId/weather", 
  checkRestaurantExists, // Middleware to check if the restaurant exists.
  async (req: Request<{ restaurantId: string }>, res, next) => {
    const { restaurantId } = req.params; // Extract the restaurant ID from the URL parameters.

    try {
      const client = await initializeRedisClient(); // Initialize Redis client.
      const weatherKey = weatherKeyById(restaurantId); // Generate the Redis key for weather data.
      
      // Check if the weather data is cached in Redis.
      const cachedWeather = await client.get(weatherKey);
      if (cachedWeather) {
        console.log("Cache Hit"); // Log a cache hit for debugging.
        return successResponse(res, JSON.parse(cachedWeather)); // Return the cached weather data.
      }

      // Retrieve the restaurant's coordinates from Redis.
      const restaurantKey = restaurantKeyById(restaurantId);
      const coords = await client.hGet(restaurantKey, "location");
      
      if (!coords) {
        return errorResponse(res, 404, "Coordinates have not been found"); // Return an error if coordinates are missing.
      }

      // Split the coordinates into longitude and latitude.
      const [lng, lat] = coords.split(",");
      
      // Fetch the weather data from the external API using the coordinates.
      const apiResponse = await fetch(
        `https://api.openweathermap.org/data/2.5/weather?units=imperial&lat=${lat}&lon=${lng}&appid=${process.env.WEATHER_API_KEY}`
      );
      
      if (apiResponse.status === 200) {
        const json = await apiResponse.json(); // Parse the API response.
        await client.set(weatherKey, JSON.stringify(json), { EX: 60 * 60 }); // Cache the weather data in Redis for 1 hour.
        return successResponse(res, json); // Return the weather data as a success response.
      }

      return errorResponse(res, 500, "Couldnt fetch weather info"); // Return an error if the API call fails.
    } catch (error) {
      next(error); // Handle errors by passing to the error middleware.
    }
  }
);

// POST route to add a review for a restaurant.
router.post(
  "/:restaurantId/reviews", 
  checkRestaurantExists, // Middleware to check if the restaurant exists.
  validate(ReviewSchema), // Validate the request body against the ReviewSchema.
  async (req: Request<{ restaurantId: string }>, res, next) => {
    const { restaurantId } = req.params; // Extract the restaurant ID from the URL parameters.
    const data = req.body as Review; // Extract the review data from the request body.

    try {
      const client = await initializeRedisClient(); // Initialize Redis client.
      const id = nanoid(); // Generate a unique ID for the review.
      
      // Prepare the review data to be stored in Redis.
      const reviewKey = reviewKeyById(id);
      const reviewData = {
        id,
        restaurantId,
        username: data.username,
        rating: data.rating,
        text: data.text,
        createdAt: new Date().toISOString(),
      };

      const reviewDetailsKey = reviewDetailsKeyById(restaurantId);
      
      // Perform the operations in parallel:
      await Promise.all([
        client.hSet(reviewKey, reviewData), // Store the review data in a hash.
        client.json.arrAppend(reviewDetailsKey, ".reviews", reviewData), // Append the review to the restaurant's review list in Redis.
        client.zIncrBy(restaurantsByRatingKey, data.rating, restaurantId), // Increment the restaurant's rating score.
      ]);

      return successResponse(res, reviewData, "Review added"); // Return success response after the review is added.
    } catch (error) {
      next(error); // Handle errors by passing to the error middleware.
    }
  }
);

export default router; // Export the router for use in the application.

```

## Cuisine.ts

```jsx
// Importing necessary modules and functions for the Express router.
import express from "express"; 
import { initializeRedisClient } from "../utils/client.js"; // Utility function to initialize Redis client.
import { cuisineKey, cuisinesKey, restaurantKeyById } from "../utils/keys.js"; // Utility functions to generate Redis keys for cuisines and restaurants.
import { successResponse } from "../utils/responses.js"; // Helper function for standard API responses.

const router = express.Router(); // Initializing the Express router.

// GET route for fetching all available cuisines.
router.get("/", async (req, res, next) => {
  try {
    const client = await initializeRedisClient(); // Initialize Redis client.
    
    // Fetch the list of all cuisines stored in the Redis set.
    const cuisines = await client.sMembers(cuisinesKey);
    
    // Return the list of cuisines as a successful response.
    return successResponse(res, cuisines);
  } catch (error) {
    next(error); // Handle errors by passing to the error middleware.
  }
});

// GET route for fetching restaurants that serve a specific cuisine.
router.get("/:cuisine", async (req, res, next) => {
  const { cuisine } = req.params; // Extract the cuisine type from the URL parameters.
  
  try {
    const client = await initializeRedisClient(); // Initialize Redis client.
    
    // Fetch the list of restaurant IDs that are associated with the given cuisine.
    const restaurantIds = await client.sMembers(cuisineKey(cuisine));
    
    // Fetch the name of each restaurant by its ID from Redis using `hGet`.
    const restaurants = await Promise.all(
      restaurantIds.map((id) => client.hGet(restaurantKeyById(id), "name"))
    );
    
    // Return the list of restaurant names as a successful response.
    return successResponse(res, restaurants);
  } catch (error) {
    next(error); // Handle errors by passing to the error middleware.
  }
});

export default router; // Export the router for use in the application.

```

### Keys.ts

```jsx
export function getKeyName(...args: string[]) {
  return `bites:${args.join(":")}`;
}

export const restaurantKeyById = (id: string) => getKeyName("restaurants", id);
export const reviewKeyById = (id: string) => getKeyName("reviews", id);
export const reviewDetailsKeyById = (id: string) =>
  getKeyName("review_details", id);
export const cuisinesKey = getKeyName("cuisines");
export const cuisineKey = (name: string) => getKeyName("cuisine", name);
export const restaurantCuisinesKeyById = (id: string) =>
  getKeyName("restaurant_cuisines", id);
export const restaurantsByRatingKey = getKeyName("restaurants_by_rating");
export const weatherKeyById = (id: string) => getKeyName("weather", id);
export const restaurantDetailsKeyById = (id: string) =>
  getKeyName("restaurant_details", id);
export const indexKey = getKeyName("idx", "restaurants");
export const bloomKey = getKeyName("bloom_restaurants");

```