<h1>Specs</h1>
<hr>
<p>
  Before talking about specs we need to know little about apis and how we use apis to communicate with third-party services. In layman terms apis are nothing but the middle man sitting between your software and the other third party softwares. We communicate and get the data using the apis. So, technically application programming interface, which is a software intermediary  that allows two applications to talk to each other. 
</p>
<h2>How to make an API request ?</h2>
<p>
  In any programming language to make an api request we basically need 3 pieces of information.
<ol>
  <li>Url</li> 
  <li>Request method</li>
  <li>Payload</li>
</ol>
We use these three pieces of information to make an api request and get the response we need. Generally, we use these apis to get the information or to post some information to the server and eventually save it in the database. For instance, let's take an example of creating an order in any market place. So our goal is to create an order and save the details to the database.
</p>

<p>
So the steps we need to follow is:
  <ol>
    <li>Send the create order payload to the create route</li>
    <li>Make a post request to the marketplace create order endpoint</li>
    <li>Now save the response to the database</li>
  </ol>

So these are the sequence of steps that we need to follow to successfully creating an order in the marketplace. So every time when you integrate the new market place we need to follow the more or less same sequence of steps for creating an order.
</p>

<h2>Automation of API request process</h2>
<p>
  So, now we know that for any event ( like create order, create a new product) we need to follow some sequence of steps. What if, instead of writing the same piece of code every time when integrating the new marketplace why don't we automate this request making process ? This idea of automating the api request process led us to create a new innovative way of making api requests i.e is specs.
</p>

<h2>Basic structure of Specs</h2>
<p>
 As we talked about steps in the above section, for any event there needs to be some sequence of steps to be followed to say that the event is successful. So, when writing specs also we mainly have two types of files. 
  <ol>
    <li>Event_steps</li>
    <li>Spec_file</li>
  </ol>
The event_steps file contains the steps that need to be followed for that particular event and it also contains information about the initial event that needs to be executed first.
</p>

<p>event_steps</p>
<pre>
  <code>
 {
   "data": {
     "title": "Title for spec file",
     "description": "A small description about the particular event",
     "name": "marketplace.method.marketplace_name.event_type",
     "start_event": "Initial event that need to be executed first",
     "steps": {
       "shipping_managment.post.ecourierz_domestic.create_shipment": {
         "success": {
           "event_key": "if success specify the next event here"
         },
         "error": {
           "event_key": "notification.error.message"
         }
       }
     }
   }
  }
  </code>
</pre>

<p>
  This is the basic structure of the event_steps file. We add the necessary steps in the steps object specifying the event name as a key and the next event that needs to be executed will be specified in the success object.
</p>

<h4>Example - 1</h4> 
<p>Let's write the event steps for get products in a shopify store.</p>
<p>Steps</p>
  <ol>
  <li>Get shopify api keys</li>
  <li>Make a get request</li>
  <li>Reverse Mapping</li>
  <li>Update to DB</li>
  </ol>
<pre>
  <code>
    {
    "data": {
        "title": "get products from shopify webstore",
        "description": "",
        "name": "products.get.shopify",
        "start_event": "webstore.get.shopify.get_api_keys",
        "steps": {
            "webstore.get.shopify.get_api_keys":{
                "success": {
                    "event_key": "webstore.get.shopify.products"
                },
                "error": {
                    "event_key": "notification.error_message"
                }
            },
            "webstore.get.shopify.products":{
                "success": {
                    "event_key": "data_transformer.reverse_mapping.shopify.products_mapping"
                },
                "error": {
                    "event_key": "notification.error_message"
                }
            },
            "data_transformer.reverse_mapping.shopify.products_mapping" :{
                "success": {
                    "event_key": "products.post.vdezi.update_shopify_products"
                },
                "error": {
                    "event_key": "notification.error_message"
                }
            },
            "products.post.vdezi.update_shopify_products" : {
                "success": {
                    "event_key": ""
                },
                "error": {
                    "event_key": "notification.error_message"
                }
            }
        }
    }
}
 </code>
</pre>
<p>
  So in the above steps we saw a new term called Reverse mapping. What is it? What is reverse mapping and what are we doing in reverse mapping? 
</p>
<code>BigCommerce</code>
<pre>
  <code>
  { 
    "id": 174, 
    "name": "1L Le Parfait Jar", 
    "type": "physical",
     "sku": "",
     "weight": 1,
     "width": 0,
     "depth": 0,
  }
  </code>
</pre>

<code>Shopify</code>
<pre>
  <code>
   {
        "product_id": 632910392,
        "position": 2,
        "created_at": "2021-06-09T20:43:00-04:00",
        "updated_at": "2021-06-09T20:43:00-04:00",
        "alt": null,
        "width": 123,
        "height": 456,
        "src": "https://cdn.shopify.com/s/files/1/0006/9093/3842/products/ipod-nano-2.png?v=1623285780",
      }
  </code>
</pre>

<p>
  Generally, we integrate different market places to vdezi platform and the client make use of our platform to get consolidated view of his products and orders for his different market places. Each market place will have a different schema for storing the information like we can see in the above example. For BigCommerce we had an id and for Shopify the same id is denoted as product_id. So, when storing this information in our database we need to have a generalized schema. We created a generalized schema and we map ( marketplace) details to our schema. Mapping the response of the request to our database format is called reverse mapping. 
</p>

<p>
 Sometimes we also do forward mapping. We call it forward mapping when we map the details from our database format to their(shopify, Bigcommerce..etc) format. Generally we do this forward mapping before making a request. We map the details to a particular marketplace payload and then send the request.
</p>

<p>
  <strong>Forward Mapping:</strong> Mapping the details from our database format to their format is called Forward mapping.
</p>

<p>
  <strong>Reverse Mapping:</strong> Mapping the details from their format to our database format is called Reverse mapping.
</p>
<h2>Spec File</h2>
<p>
  As I discussed earlier, we mainly had two types of files. One is event_steps. We discussed how to write the event_steps. Now lets see how to write the actual spec file.
</p>
<h4>Basic Structutre of specs</h4>
<p>
  Actual spec file will contain mainly eight keys
  <ol>
    <li>name</li>
    <li>event_type</li>
    <li>description</li>
    <li>parameters</li>
    <li>outputs</li>
     <li>response</li>
     <li>request_configuration</li>
     <li>request</li>
   </ol>
 <pre>
  <code>
  {
 "data": {
   "name":"name of the spec file",
   "event_type":"marketplace.method.marketplace_name.event_type",
   "description":"small description about the spec",
   "parameters":{},
   "outputs":{},
   "response":{},
   "request_configuration":{},
   "request":{}
 }
}
  </code>
</pre>
<p/>
<h2>Field Descriptions</h2>
<p>
  <strong>name:</strong>  We specify the name of the spec 

  <strong>event_type :</strong> Here we have two types of events
  <ol>
    <li>api_call</li>
    <li>data_mapping</li>

  api_call event is nothing but making an api request and data_mapping event says that we are writing spec for mapping events like forward mapping or reverse mapping.

  <strong>description:</strong> We write a small description about the spec.

  <strong>parameters:</strong> In parameters we either refer to the previous event data or the data that is needed to run that particular event.

  <strong>outputs:</strong> In the outputs we have two keys: success and error. We save the data of success or error based on the response key conditions. 

  <strong>response:</strong> response will have conditions to check whether the request is successful or not. If the response is successful then it will save whatever data in outputs -> success or else it will save the output error.

  <strong>request_configuration:</strong>  request_configuration will have different types of request like single_request, paginated_request..etc.

  <strong>request:</strong>  the request contains all the host, url endpoint, api request method, protocol and also headers, params and body information.
</p>



  

  
