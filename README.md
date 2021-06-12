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


  
