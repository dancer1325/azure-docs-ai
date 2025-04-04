---
author: HeidiSteen
ms.author: heidist
ms.service: azure-ai-search
ms.custom:
  - ignite-2023
ms.topic: include
ms.date: 10/07/2024
---

Build a Node.js application using the [@azure/search-documents](/javascript/api/overview/azure/search-documents-readme) library to create, load, and query a search index. 

Alternatively, you can [download the source code](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/main/quickstart) to start with a finished project or follow these steps to create your own.

#### Set up your environment

We used the following tools to create this quickstart.

+ [Visual Studio Code](https://code.visualstudio.com), which has built-in support for creating JavaScript apps

+ [Node.js LTS](https://nodejs.org) and [npm](https://www.npmjs.com)

* [TypeScript](https://www.typescriptlang.org/download/)

#### Create the project

1. Start Visual Studio Code.

1. Open the [Command Palette](https://code.visualstudio.com/docs/getstarted/userinterface#_command-palette) by using **Ctrl+Shift+P** and open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).

1. Create a development directory, giving it the name *quickstart*:

    ```cmd
    mkdir quickstart
    cd quickstart
    ```

1. Initialize an empty project with npm by running the following command. To fully initialize the project, press Enter multiple times to accept the default values, except for the License, which you should set to *MIT*. 

    ```cmd
    npm init
    ```

1. Install `@azure/search-documents`, the [JavaScript/TypeScript SDK for Azure AI Search](/javascript/api/overview/azure/search-documents-readme). 

    ```cmd
    npm install @azure/search-documents
    ```

1. Install `dotenv`, which is used to import the environment variables such as your search service name and API key.

    ```cmd
    npm install dotenv
    ```

1. Navigate to the *quickstart* directory, then confirm that you've configured the project and its dependencies by checking that your *package.json* file looks similar to the following json:

    ```json
    {
      "name": "quickstart",
      "version": "1.0.0",
      "description": "Azure AI Search Quickstart",
      "main": "index.js",
      "scripts": {
        "test": "echo \"Error: no test specified\" && exit 1"
      },
      "keywords": [
        "Azure",
        "Search"
      ],
      "author": "Your Name",
      "license": "MIT",
      "dependencies": {
        "@azure/search-documents": "^12.1.0",
        "dotenv": "^16.4.5"
      }
    }
    ```

1. Create a file *.env* to hold your search service parameters:

    ```
    SEARCH_API_KEY=<YOUR-SEARCH-ADMIN-API-KEY>
    SEARCH_API_ENDPOINT=<YOUR-SEARCH-SERVICE-URL>
    ```

Replace the `YOUR-SEARCH-SERVICE-URL` value with the name of your search service endpoint URL. Replace `<YOUR-SEARCH-ADMIN-API-KEY>` with the admin key you recorded earlier. 

#### Create index.ts file

Next we create an *index.ts* file, which is the main file that hosts our code.

At the top of this file, we import the `@azure/search-documents` library:

```typescript
import {
    AzureKeyCredential,
    ComplexField,
    odata,
    SearchClient,
    SearchFieldArray,
    SearchIndex,
    SearchIndexClient,
    SearchSuggester,
    SimpleField
} from "@azure/search-documents";
```

Next, we need to require the `dotenv` package to read in the parameters from the *.env* file as follows:

```typescript
// Load the .env file if it exists
import dotenv from 'dotenv';
dotenv.config();

// Getting endpoint and apiKey from .env file
const endpoint = process.env.SEARCH_API_ENDPOINT || "";
const apiKey = process.env.SEARCH_API_KEY || "";
```

With our imports and environment variables in place, we're ready to define the main function.

Most of the functionality in the SDK is asynchronous so we make our main function `async`. We also include a `main().catch()` below the main function to catch and log any errors encountered:

```typescript
async function main() {
    console.log(`Running Azure AI Search JavaScript quickstart...`);
    if (!endpoint || !apiKey) {
        console.log("Make sure to set valid values for endpoint and apiKey with proper authorization.");
        return;
    }

    // remaining quickstart code will go here
}

main().catch((err) => {
    console.error("The sample encountered an error:", err);
});
```

With that in place, we're ready to create an index.

#### Create index

Create a file *hotels_quickstart_index.json*. This file defines how Azure AI Search works with the documents you'll be loading in the next step. Each field will be identified by a `name` and have a specified `type`. Each field also has a series of index attributes that specify whether Azure AI Search can search, filter, sort, and facet upon the field. Most of the fields are simple data types, but some, like `AddressType` are complex types that allow you to create rich data structures in your index. You can read more about [supported data types](/rest/api/searchservice/supported-data-types) and index attributes described in [Create Index (REST)](/rest/api/searchservice/indexes/create). 

Add the following content to *hotels_quickstart_index.json* or [download the file](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/main/quickstart/hotels_quickstart_index.json).

```json
{
    "name": "hotels-quickstart",
    "fields": [
        {
            "name": "HotelId",
            "type": "Edm.String",
            "key": true,
            "filterable": true
        },
        {
            "name": "HotelName",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "sortable": true,
            "facetable": false
        },
        {
            "name": "Description",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "sortable": false,
            "facetable": false,
            "analyzerName": "en.lucene"
        },
        {
            "name": "Description_fr",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "sortable": false,
            "facetable": false,
            "analyzerName": "fr.lucene"
        },
        {
            "name": "Category",
            "type": "Edm.String",
            "searchable": true,
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "Tags",
            "type": "Collection(Edm.String)",
            "searchable": true,
            "filterable": true,
            "sortable": false,
            "facetable": true
        },
        {
            "name": "ParkingIncluded",
            "type": "Edm.Boolean",
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "LastRenovationDate",
            "type": "Edm.DateTimeOffset",
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "Rating",
            "type": "Edm.Double",
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "Address",
            "type": "Edm.ComplexType",
            "fields": [
                {
                    "name": "StreetAddress",
                    "type": "Edm.String",
                    "filterable": false,
                    "sortable": false,
                    "facetable": false,
                    "searchable": true
                },
                {
                    "name": "City",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
                {
                    "name": "StateProvince",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
                {
                    "name": "PostalCode",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
                {
                    "name": "Country",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                }
            ]
        }
    ],
    "suggesters": [
        {
            "name": "sg",
            "searchMode": "analyzingInfixMatching",
            "sourceFields": [
                "HotelName"
            ]
        }
    ]
}
```

With our index definition in place, we want to import *hotels_quickstart_index.json* at the top of *index.ts* so the main function can access the index definition.

```typescript
// Importing the index definition and sample data
import indexDefinition from './hotels_quickstart_index.json';

interface HotelIndexDefinition {
    name: string;
    fields: SimpleField[] | ComplexField[];
    suggesters: SearchSuggester[];
};
const hotelIndexDefinition: HotelIndexDefinition = indexDefinition as HotelIndexDefinition;
```

Within the main function, we then create a `SearchIndexClient`, which is used to create and manage indexes for Azure AI Search. 

```javascript
const indexClient = new SearchIndexClient(endpoint, new AzureKeyCredential(apiKey));
```

Next, we want to delete the index if it already exists. This operation is a common practice for test/demo code.

We do this by defining a simple function that tries to delete the index.

```typescript
async function deleteIndexIfExists(indexClient: SearchIndexClient, indexName: string): Promise<void> {
    try {
        await indexClient.deleteIndex(indexName);
        console.log('Deleting index...');
    } catch {
        console.log('Index does not exist yet.');
    }
}
```

To run the function, we extract the index name from the index definition and pass the `indexName` along with the `indexClient` to the `deleteIndexIfExists()` function.

```typescript
// Getting the name of the index from the index definition
const indexName: string = hotelIndexDefinition.name;

console.log('Checking if index exists...');
await deleteIndexIfExists(indexClient, indexName);
```

After that, we're ready to create the index with the `createIndex()` method.

```typescript
console.log('Creating index...');
let index = await indexClient.createIndex(hotelIndexDefinition);

console.log(`Index named ${index.name} has been created.`);
```

#### Run the sample

At this point, you're ready to build and run the sample. Use a terminal window to run the following commands to build your source with `tsc` then run your source with `node`:

```cmd
tsc
node index.ts
```

If you [downloaded the source code](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/main/quickstart) and haven't installed the required packages yet, run `npm install` first.

You should see a series of messages describing the actions being taken by the program. 

Open the **Overview** of your search service in the Azure portal. Select the **Indexes** tab. You should see something like the following example:

:::image type="content" source="../../media/search-get-started-javascript/create-index-no-data.png" alt-text="Screenshot of Azure portal, search service Overview, Indexes tab." border="false":::

In the next step, you'll add data to index. 

#### Load documents 

In Azure AI Search, documents are data structures that are both inputs to indexing and outputs from queries. You can push such data to the index or use an [indexer](/azure/search/search-indexer-overview). In this case, we'll programatically push the documents to the index.

Document inputs might be rows in a database, blobs in Blob storage, or, as in this sample, JSON documents on disk. You can either download [hotels.json](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/main/quickstart/hotels.json) or create your own *hotels.json* file with the following content:

```json
{
    "value": [
        {
            "HotelId": "1",
            "HotelName": "Secret Point Motel",
            "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
            "Description_fr": "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
            "Category": "Boutique",
            "Tags": ["pool", "air conditioning", "concierge"],
            "ParkingIncluded": false,
            "LastRenovationDate": "1970-01-18T00:00:00Z",
            "Rating": 3.6,
            "Address": {
                "StreetAddress": "677 5th Ave",
                "City": "New York",
                "StateProvince": "NY",
                "PostalCode": "10022"
            }
        },
        {
            "HotelId": "2",
            "HotelName": "Twin Dome Motel",
            "Description": "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
            "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
            "Category": "Boutique",
            "Tags": ["pool", "free wifi", "concierge"],
            "ParkingIncluded": "false",
            "LastRenovationDate": "1979-02-18T00:00:00Z",
            "Rating": 3.6,
            "Address": {
                "StreetAddress": "140 University Town Center Dr",
                "City": "Sarasota",
                "StateProvince": "FL",
                "PostalCode": "34243"
            }
        },
        {
            "HotelId": "3",
            "HotelName": "Triple Landscape Hotel",
            "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
            "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
            "Category": "Resort and Spa",
            "Tags": ["air conditioning", "bar", "continental breakfast"],
            "ParkingIncluded": "true",
            "LastRenovationDate": "2015-09-20T00:00:00Z",
            "Rating": 4.8,
            "Address": {
                "StreetAddress": "3393 Peachtree Rd",
                "City": "Atlanta",
                "StateProvince": "GA",
                "PostalCode": "30326"
            }
        },
        {
            "HotelId": "4",
            "HotelName": "Sublime Cliff Hotel",
            "Description": "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
            "Description_fr": "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
            "Category": "Boutique",
            "Tags": ["concierge", "view", "24-hour front desk service"],
            "ParkingIncluded": true,
            "LastRenovationDate": "1960-02-06T00:00:00Z",
            "Rating": 4.6,
            "Address": {
                "StreetAddress": "7400 San Pedro Ave",
                "City": "San Antonio",
                "StateProvince": "TX",
                "PostalCode": "78216"
            }
        }
    ]
}
```

Similar to what we did with the indexDefinition, we also need to import `hotels.json` at the top of *index.ts* so that the data can be accessed in our main function.

```typescript
import hotelData from './hotels.json';

interface Hotel {
    HotelId: string;
    HotelName: string;
    Description: string;
    Description_fr: string;
    Category: string;
    Tags: string[];
    ParkingIncluded: string | boolean;
    LastRenovationDate: string;
    Rating: number;
    Address: {
        StreetAddress: string;
        City: string;
        StateProvince: string;
        PostalCode: string;
    };
};

const hotels: Hotel[] = hotelData["value"];
```

To index data into the search index, we now need to create a `SearchClient`. While the `SearchIndexClient` is used to create and manage an index, the `SearchClient` is used to upload documents and query the index.

There are two ways to create a `SearchClient`. The first option is to create a `SearchClient` from scratch:

```typescript
 const searchClient = new SearchClient<Hotel>(endpoint, indexName, new AzureKeyCredential(apiKey));
```

Alternatively, you can use the `getSearchClient()` method of the `SearchIndexClient` to create the `SearchClient`:

```typescript
const searchClient = indexClient.getSearchClient<Hotel>(indexName);
```

Now that the client is defined, upload the documents into the search index. In this case, we use the `mergeOrUploadDocuments()` method, which uploads the documents or merges them with an existing document if a document with the same key already exists. Then check that the operation succeeded because at least the first document exists.

```typescript
console.log("Uploading documents...");
const indexDocumentsResult = await searchClient.mergeOrUploadDocuments(hotels);

console.log(`Index operations succeeded: ${JSON.stringify(indexDocumentsResult.results[0].succeeded)}`);
```

Run the program again with `tsc && node index.ts`. You should see a slightly different set of messages from those you saw in Step 1. This time, the index *does* exist, and you should see a message about deleting it before the app creates the new index and posts data to it. 

Before we run the queries in the next step, define a function to have the program wait for one second. This is done just for test/demo purposes to ensure the indexing finishes and that the documents are available in the index for our queries.

```typescript
function sleep(ms: number): Promise<void> {
    return new Promise((resolve) => setTimeout(resolve, ms));
}
```

To have the program wait for one second, call the `sleep` function:

```typescript
sleep(1000);
```

#### Search an index

With an index created and documents uploaded, you're ready to send queries to the index. In this section, we send five different queries to the search index to demonstrate different pieces of query functionality available to you.

The queries are written in a `sendQueries()` function that we call in the main function as follows:

```typescript
await sendQueries(searchClient);
```

Queries are sent using the `search()` method of `searchClient`. The first parameter is the search text and the second parameter specifies search options.

The first query searches `*`, which is equivalent to searching everything and selects three of the fields in the index. It's a best practice to only `select` the fields you need because pulling back unnecessary data can add latency to your queries.

The `searchOptions` for this query also has `includeTotalCount` set to `true`, which will return the number of matching results found.

```typescript
async function sendQueries(
    searchClient: SearchClient<Hotel>
): Promise<void> {

    // Query 1
    console.log('Query #1 - search everything:');
    const selectFields: SearchFieldArray<Hotel> = [
        "HotelId",
        "HotelName",
        "Rating",
    ];
    const searchOptions1 = { 
        includeTotalCount: true, 
        select: selectFields 
    };

    let searchResults = await searchClient.search("*", searchOptions1);
    for await (const result of searchResults.results) {
        console.log(`${JSON.stringify(result.document)}`);
    }
    console.log(`Result count: ${searchResults.count}`);

    // remaining queries go here
}
```

The remaining queries outlined below should also be added to the `sendQueries()` function. They're separated here for readability.

In the next query, we specify the search term `"wifi"` and also include a filter to only return results where the state is equal to `'FL'`. Results are also ordered by the Hotel's `Rating`.

```typescript
console.log('Query #2 - search with filter, orderBy, and select:');
let state = 'FL';
const searchOptions2 = {
    filter: odata`Address/StateProvince eq ${state}`,
    orderBy: ["Rating desc"],
    select: selectFields
};
searchResults = await searchClient.search("wifi", searchOptions2);
for await (const result of searchResults.results) {
    console.log(`${JSON.stringify(result.document)}`);
}
```

Next, the search is limited to a single searchable field using the `searchFields` parameter. This approach is a great option to make your query more efficient if you know you're only interested in matches in certain fields. 

```typescript
console.log('Query #3 - limit searchFields:');
const searchOptions3 = {
    select: selectFields,
    searchFields: ["HotelName"] as const
};

searchResults = await searchClient.search("sublime cliff", searchOptions3);
for await (const result of searchResults.results) {
    console.log(`${JSON.stringify(result.document)}`);
}
```

Another common option to include in a query is `facets`. Facets allow you to provide self-directed drilldown from the results in your UI. The facets results can be turned into checkboxes in the result pane. 

```typescript
console.log('Query #4 - limit searchFields and use facets:');
const searchOptions4 = {
    facets: ["Category"],
    select: selectFields,
    searchFields: ["HotelName"] as const
};

searchResults = await searchClient.search("*", searchOptions4);
for await (const result of searchResults.results) {
    console.log(`${JSON.stringify(result.document)}`);
}
```

The final query uses the `getDocument()` method of the `searchClient`. This allows you to efficiently retrieve a document by its key. 

```typescript
console.log('Query #5 - Lookup document:');
let documentResult = await searchClient.getDocument('3')
console.log(`HotelId: ${documentResult.HotelId}; HotelName: ${documentResult.HotelName}`)
```

#### Rerun the sample

Build and run the program with `tsc && node index.ts`. Now, in addition to the previous steps, the queries are sent and the results written to the console.
