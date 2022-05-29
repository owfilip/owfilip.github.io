# Use functional approach instead of conditional statements

I am quite certain each of you encountred or even wrote code with cascade of conditional statements. In this post we are going to review 2 most popular and probably first-choise approaches. While they just fine for simple cases, while the application grows, they become more and more complex, leghty and eventually harder to comprehend. We are going to take example of simple request handler. The whole idea is to write code which does not contain any conditional statement, yet is equally readable and most of all, much better maintainable. All code samples are in Typescript.

Here is simplification of request object:

    interface Request {
      method: string, 
      url: string;
    }

We are going to handle the following requests:

    GET /recipes
    GET /products
    POST /recipes

Request objects:
    
    const getRecipes: Request = { method: 'GET', url: '/recipes' }
    const getProducts: Request = { method: 'GET', url: '/products' }
    const postRecipe: Request = { method: 'POST', url: '/recipes' }

Let's also define request, which is not handled by our application:

    const unhandled: Request = { method: 'PUT', url: '/product' }

## If-else-if

Let's start with most popular if-else-if chain.

    if (request.method === 'GET' && request.url === '/recipes') {
      console.log('List recipes ==> ', request);
    } else if (request.method === 'GET' && request.url === '/products') {
      console.log('List available products ==> ', request);
    } else if (request.method == 'POST' && request.url === '/products') {
      console.log('Create recipe ==> ', request);
    } else {
      console.log('Unhandled request ==> ', request);
    }

As you see, this might get quite legthy. Another drawback is that for a developer it might take a while to find the right conditional. As a result, the whole code must be in in a single, quite leghty method. 


## Switch-case

Now let's consider another approach, using switch-case statement.

    const requestKey = `${request.method}:${request.url}`;

    switch (requestKey) {
      case 'GET:/recipes': {
        console.log('List recipes ==> ', request);
        break;
      }
      case 'GET:/products': {
        console.log('List available products ==> ', request);
        break;
      }
      case 'POST:/recipes': {
        console.log('Create recipe ==> ', request);
        break;
      }
      default: {
        console.log('Unhandled request ==> ', request);
      }
    }

First we must create a `key` for each `case`, so that `switch` can pick up the right code. And do not forget about `break`! This approach is hardly better and the method handler would become even longer comparing to if-else-if approach.


## Functional approach

Hopefully neat feature of Javascript is calling properties by indexers. What is more, the properties might be references to functions. That gives us a new way to excecute desired code, depending on certain condition, without actualy using conditional statements, like if-else or switch-case.

Let's review the above code. First extract body of each into a function.

    const handleGetRecipes = (request: Request) => { console.log('List recipes ==> ', request); return; }
    const handleGetProducts = (request: Request) => { console.log('List available products ==> ', request); return; }
    const handlePostRecipe = (request: Request) => { console.log('Create recipe ==> ', request); return; }
    const unhandledRequest = (request: Request) => { console.log('Unhandled request ==> ', request); return };

While this is obviusly oversimplified, now we operate on method references. So, how to handler each case without acually using a single conditional? Start by definig a map. Similar as with switch-case, we define a `key` for each handler, by combining request method and request url:

    const requestsMap: { [requestKey: string]: Function} = {
      'GET:/recipes': handleGetRecipes,
      'GET:/products': handleGetProducts,
      'POST:/recipes': handlePostRecipe
    }

Finally we define `handler`:

    function handler(request: Request): void {
      const requestKey = `${request.method}:${request.url}`;
      return requestsMap[requestKey] ? requestsMap[requestKey](request) : unhandledRequest(request);
    };

What is going on in the above code? We make use of request map, where `key` is our `requestKey`, a combination method http verb and url, while `value` is reference to relevant endpoint handler. However, this time we execute relevant handler direcly on `requestMap`, thanks to Javascript feature, which enables to call properties of object by indexer. This way any new endpoint handler would require, apart from adding method handler function, one entry in `requestMap`. Impossible to be more concise.

Repository with code used in this post can be foudn here: https://github.com/owfilip/node-start/tree/request_handler


