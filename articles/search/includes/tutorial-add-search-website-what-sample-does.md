---
ms.topic: include
ms.date: 07/18/2023
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
---
This sample website provides access to a catalog of 10,000 books. A user can search the catalog by entering text in the search bar. While the user enters text, the website uses the search index's suggest feature to complete the text. Once the query finishes, the list of books is displayed with a portion of the details. A user can select a book to see all the details, stored in the search index, of the book. 

:::image type="content" source="../media/tutorial-javascript-overview/cognitive-search-enabled-book-website.png" alt-text="This sample website provides access to a catalog of 10,000 books. A user can search the catalog by entering text in the search bar. While the user enters text, the website uses the search index's suggest feature to complete the text. Once the search finishes, the list of books is displayed with a portion of the details. A user can select a book to see all the details, stored in the search index, of the book.":::

The search experience includes: 

* Search – provides search functionality for the application.
* Suggest – provides suggestions as the user is typing in the search bar.
* Document Lookup – looks up a document by ID to retrieve all of its contents for the details page.
