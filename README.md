# JPMC-tech-task-3-PY3

JPMorgan Chase Software Engineering virtual internship task -3

I am doing virtual internship on JPMorgan Chase Software Engineering for that purpose i created this repo and i am going to share how finsish my task that was given by JPMorgan Chase Software Engineering

before going to task i like to share how i get this internship.

I get the internship on this website insidesherpa <a href="https://www.insidesherpa.com/"> click here</a>
other than JPMorgan Chase Software Engineering lot's of company offer free virtual internship including Microsoft,......

This repo for the task 3<br/>
<hr>
<h3> WARNING: My English not so good. so applogize me </h3>
<hr>
<h1>Task 3</h1>
<p>
Here is the background information on our task
Being able to access and  adjust data feeds is critical to any trading analysis and stock price monitoring. From the previous tasks, we now have the adjusted data set up on your systems and being piped into Perspective.
</p>
<p>
For traders to have a complete picture of all the trading strategies being monitored, several screens typically display an assortment of live and historical data at their workstation.
</p>
<p>
Given there is a lot of information and data being produced at once, visualizing data in a clear manner with UI/UX considerations accounted for is critical to providing traders with the tools to improve their performance.
</p>
<p><br/>
* Understanding the finance and trading part is not required for this task.<br/>

* Being familiar with python scripting language, command line basics, javascript, react and typescript are not required for this task too as you will be guided in this exercise<br/>
</p>
<br/>
<p>
For the third module of this project will need you to accomplish the following:
</p>
<ol>
  <li>Set up your system by downloading the necessary files, tools and dependencies. </li><br/>
 <li>Modify the typescript files in repository to make the web application behave in the expected manner</li><br/>
 <li>Generate a patch file of the changes you made.</li><br/>
</ol>

<hr>

<h1>Step1:Set up your system </h1>
 how to setup your enviroment for this task <a href="https://insidesherpa.s3.amazonaws.com/vinternships/companyassets/Sj7temL583QAYpHXD/setup_devenv_m1_v6.pdf" >Click Here</a> <br/>
 <p> how to run the code </p>
 <pre>
 python datafeed/server3.py // it start the server
 npm install  // it install the dependency pkg
 npm start  // it start our app
 </pre> 
 <hr>
 
<h1>Step2:Modify the typescript files in repository to make the web application behave in the expected manner</h1>
<p>
 Before fixing the broken Typescript files. we need to know some basic that are </p>
 <ol>
 <li> The file structure of the program </li>
 <pre>
├───datafeed ->which the server3.py and test(excel file ) 
├───public  ->index.html (our main html code is here the html code generated by a react is insert here while excuting the code )
└───src 
      |--index.tsx ->  this typescript program is responsible for render the whole app
      |--index.css -> this css file for index.tsx  for styling purpose
      |--App.tsx  ->this is the main typescript code 
      |--App.test.js   -> this for testing purpose  
      |--App.css -> css file for our main app.tsx(typescript)
      |--DataStreamer.ts  -> this typescript program is responsible for get data from the server
      |--Graph.tsx  -> this  typescript program is responsible for drawing a graph on the browser
      |--Graph.css  -> this css file for graph.tsx 
</pre>

<p>There are two things we have to achieve here to complete this task</p>
<ol>
<li>We now want to make this graph more useful to traders by making it
track the ratio between two stocks over time and NOT the two stocks’
  top_ask_price over time.</li><br/>
<li>As mentioned before, traders want to monitor the ratio of two stocks
against a historical correlation with upper and lower thresholds/bounds.
This can help them determine a trading opportunity.That said, we also want
to make this graph plot those upper and lower thresholds and show when
they get crossed by the ratio of the stock </li><br/>
</ol>
<p>To achieve this we have to change (2) files</p><br/>
1.src/Graph.tsx <br/>
2.src/DataManipulator.ts<br/>
<br/>

<h3>Making changes in `Graph.tsx`</h3>
<p>
● App.tsx is the main app (typescript) file of our client side react application.
 <br/><br/>
● To accomplish our first objective, we must first make changes to the
`Graph.tsx` file. Recall, this is the file that takes care of how the Graph
component of our App will be rendered and react to the state changes that
occur within the App.
<br/><br/>
● We’re not starting from a static graph anymore and we’re basically jumping off
from where we’ve finished in task 2. So the changes we’ll be making here
aren’t going to be as much. What we want to do here is to have one main line
tracking the ratio of two stocks and be able to plot upper and lower bounds
too.
<br/><br/>
</p>
<p>
To do this, we need to modify `componentDidMount` method. Recall, the
componentDidMount() method runs after the component output has been
rendered to the DOM.
 <br/><br/>
● In this method, we first have to modify the schema object as that will dictate
how we’ll be able to configure the Perspective table view of our graph.
<br/><br/>
</p>
<p>
 so the code look like this 
</p><br/>
<h4> Before change</h4>
<pre>
componentDidMount() {
    ....
    ....
    const schema = {
      stock: 'string',
      top_ask_price: 'float',
      top_bid_price: 'float',
      timestamp: 'date',
    };
 }
</pre>
<h4>After change</h4>
<pre>
componentDidMount() {
    ....
    ....
    /*price_abc and price_def for calculate ratio */
    const schema = {
      price_abc:'float', 
      price_def: 'float',
      ratio: 'float',
      timestamp: 'date',
      upper_bound:'float',
      lower_bound:'float',
      trigger_alert:'float',
   };
}
</pre>
<p>
Since we don’t want to distinguish between two stocks now, but instead want to track their
ratios, we made sure to add the ratio field. Since we also wanted to track upper_bound,
lower_bound, and the moment when these bounds are crossed i.e. trigger_alert, we had to
add those fields too.
</p>

<p>
 Finally, the reason we added price_abc and price_def is just because these were necessary
to get the ratio as you will see later. We won’t be configuring the graph to show them anyway.
</p>
<p>
since we’re tracking all of this with respect to time, timestamp is going to be there
</p>
<p>
Next, we going to configure our graph you will need to modify/add more attributes to the
element.
<p>
 so the code look like this 
</p><br/>
<h4> Before change</h4>
<pre>
componentDidMount() {
    ....
    ....
    if (this.table) {
      // Load the `table` in the `<perspective-viewer>` DOM reference.
      elem.load(this.table);
      elem.setAttribute('view', 'y_line');
      elem.setAttribute('column-pivots', '["stock"]');
      elem.setAttribute('row-pivots', '["timestamp"]');
      elem.setAttribute('columns', '["top_ask_price"]');
      elem.setAttribute('aggregates', JSON.stringify({
        stock: 'distinctcount',
        top_ask_price: 'avg',
        top_bid_price: 'avg',
        timestamp: 'distinct count',
      }));
    }
 }
</pre>
<h4>After change</h4>
<pre>
componentDidMount() {
    ....
    ....
    if (this.table) {
      // Load the `table` in the `<perspective-viewer>` DOM reference.
      elem.load(this.table);
      /* changed some the attribute for table*/ 
      elem.setAttribute('view', 'y_line');
      elem.setAttribute('row-pivots', '["timestamp"]');
      /* filter the columns that we only want to display ratio lowerbound,upperbound,triggeralert in graph*/
      elem.setAttribute('columns', '["ratio","lower_bound","upper_bound","trigger_alert"]');
      elem.setAttribute('aggregates', JSON.stringify({
      price_abc:'avg',
      price_def:'avg',
      ratio:'avg',
      timestamp: 'distinct count',
      upper_bound: 'avg',
      lower_bound: 'avg',
      trigger_alert:'avg',
      }));
    }
}
</pre>
<p>
Finally, we have to make a slight update in the componentDidUpdate method.
This method is another component lifecycle method that gets executed
whenever the component updates, i.e. when the graph gets updated in our
case. The change we want to make is on the argument we put in
this.table.update.
</p>
<p>
 so the code look like this 
</p><br/>
<h4> Before change</h4>
<pre>
 componentDidUpdate() {
    if (this.table) {
      this.table.update(
        DataManipulator.generateRow(this.props.data),
      );
    }
  }
</pre>
<h4>After change</h4>
<pre>
 componentDidUpdate() {
    if (this.table) {
    /* passing server data to generaterow function  and store the return value as array and pass to table update function*/
      this.table.update([DataManipulator.generateRow(this.props.data)] );
    }
  }
</pre>

<h3>Making changes in `DataManipulator.ts`</h3>
<p>
● To fully achieve our goal in this task, we have to make some modifications in
the DataManipulator.ts file. This file will be responsible for processing the raw
stock data we’ve received from the server before it throws it back to the Graph
component’s table to render. Initially, it’s not really doing any processing hence
we were able to keep the status quo from the finished product in task 2
<br/><br/>
● The first thing we have to modify in this file is the Row interface. If you see,
the initial setting of the Row interface is almost the same as the old schema in
Graph.tsx before we updated it. So now, we have to update it to match the
new schema. 
<br/><br/>
<p>
 so the code look like this 
</p><br/>
<h4> Before change</h4>
<pre>
 export interface Row {
  stock: string,
  top_ask_price: number,
  timestamp: Date,
}
</pre>
<h4>After change</h4>
<pre>
/*changed interface row  by add new value(property) and remove some value*/
export interface Row {
 price_abc:number,
 price_def:number,
 ratio:number,
 timestamp: Date,
 upper_bound: number,
 lower_bound: number,
 trigger_alert:number | undefined,
}
/*trigger_alert may number or undefined because that only render if the ratio go above upperbound or go below the lowerbound*/
</pre>
<p>
Finally, we have to update the generateRow function of the DataManipulator
class to properly process the raw server data passed to it so that it can return
the processed data which will be rendered by the Graph component’s table.</p>
<br><br>
<p>
● Here we can compute for price_abc and price_def properly. next we  also compute for ratio using the two
computed prices and  set lower and upperbounds as well as trigger_alert.
</p><br><br>

<p>
 so the code look like this 
</p><br/>
<h4> Before change</h4>
<pre>
export class DataManipulator {
  static generateRow(serverResponds: ServerRespond[]): Row[] {
    return serverResponds.map((el: any) => {
      return {
        stock: el.stock,
        top_ask_price: el.top_ask && el.top_ask.price || 0,
        timestamp: el.timestamp,
      };
    })
  }
}
</pre>
<h4>After change</h4>

<pre>
export class DataManipulator {
 /* array of Row objects to just a single Row object This change explains why we also adjusted the argument we passed to table.update in Graph.tsx earlier */
  static generateRow(serverResponds: ServerRespond[]): Row {
    /* calculate the price of trade , ratio, upperbound,lowerboud*/
    const priceABC=(serverResponds[0].top_ask.price+serverResponds[0].top_bid.price)/2;
    const priceDEF=(serverResponds[1].top_ask.price+serverResponds[1].top_bid.price)/2;
    const ratio=priceABC/priceDEF;
    const upperbound=1+0.05;
    const lowerbound=1-0.05;
    /* assign greater timestamp */
    const timestamp=serverResponds[0].timestamp > serverResponds[1].timestamp ?serverResponds[0].timestamp:serverResponds[1].timestamp;
    /* assign ratio value to triggeralert if the ratio between lowerbound and upperbound alse assign undefined*/
    const triggeralert=(ratio >upperbound || ratio<lowerbound ) ? ratio:undefined;
    /*return the data to graph componentDidUpdate function*/
    return {price_abc:priceABC,
        price_def:priceDEF,
        ratio:ratio,
        timestamp:timestamp,
        upper_bound:upperbound,
        lower_bound :lowerbound,
        trigger_alert:triggeralert,
      };    
  }
}
</pre>

<p>
Changes in DataManipulator.ts are done. By now we should have  accomplished all the objectives of the task.
</p>
<hr/>
<h1>Step3.Generate a patch file of the changes you made.</h1>
<p>
Follow this steps to create patch file 
open a terminal, enter the repository via the terminal you opened (via the cd
<repo_name_here> aka change directory command) and do the following commands
(one line, one command)</p><br/>
<ol>
<li>git add -A</li>
<li>git config user.email "your_email_address"</li>
<li>git config user.name "your_name"</li>
<li>git commit -m 'Create Patch File'</li>
<li>git format-patch -1 HEAD</li>
</ol>
<br/>
<br/>
The final command, i.e. git format-patch -1 HEAD, should produce the .patch file
you’d want to submit to complete this module. It will be located in the directory
where you executed the command.
<hr>

<h1>Resource</h1>
<p> Finally i am going to share the resource i used for this task</p>
<ol>
  <li>Node js resource</li>
  <ol>
    <li> <a href="https://www.youtube.com/watch?v=fBNz5xF-Kx4">YouTube videos</a></li>
    <li> <a href="https://www.youtube.com/watch?v=vJEO57B05Sg">YouTube videos</a></li>
    <li> <a href="https://www.youtube.com/watch?v=TlB_eWDSMt4">YouTube videos</a></li>
  </ol>
    

<li>React js resource</li>
  <ol>
    <li> <a href="https://www.youtube.com/watch?v=sBws8MSXN7A&t=682s">YouTube videos</a></li>
    <li> <a href="https://www.youtube.com/watch?v=nvHeB32ICDM">YouTube videos</a></li>
    <li> <a href="https://www.youtube.com/watch?v=Ke90Tje7VS0">YouTube videos</a></li>
  </ol>
  
<li>Type Script resource</li>
  <ol>
    <li> <a href="https://www.youtube.com/watch?v=NjN00cM18Z4">YouTube videos</a></li>
    <li> <a href="https://www.youtube.com/watch?v=rAy_3SIqT-E">YouTube videos</a></li>
    <li> <a href="https://www.youtube.com/watch?v=ahCwqrYpIuM">YouTube videos</a></li>
  </ol><br/>
  <p>Last but not least <a href="https://www.w3schools.com/">W3School</a></p>
  </ol>
  
<hr>
<h1> Conclusion</h1>

<p> What are the things i learned from this task</p>
<ol>
  <li> Node js fundamental</li>
  <li> React js fundamental</li>
  <li> Type Script fundamental</li>
</ol><br/>
<hr> 
<p> ALL THE BEST</p><br/>
<hr>
