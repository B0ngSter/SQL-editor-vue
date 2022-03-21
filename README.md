Hello there!!
I've used the same data you guys provided, all those csv files are there in static folder.
I've used codemirror library to use the sql editor component.
since the SQL can not be executed using javascript, backend is definatly needed to execute the queries properly, I wrote down the basic js to execute the select query. (just the select queries)
Example:- SELECT customerID, employeeID, orderDate FROM  orders;
instead of writing down the SQL we can also use the UI to execute the query, click on the tables (on left hand side) and check the required rows and simply press execute button. the result will be presented on the bottom.


Page load Time:- aprox 700ms~900ms on first load, after refreshing the load time will be improved.
I used the lighthouse tool also, got 98% performance.
I used window.performance.timing.domContentLoadedEventEnd - window.performance.timing.navigationStart to measure the Page load time. I didn't know about this method, I found it on stackoverflow.
at first the Page load time was around 2 seconds, later I minified the CSS/JS using nuxt.js predefined method (nuxt.config.js file)
also I imported the only components from node-module which are in use.
which helped me improve the Page load time significantly.
here is the link where the site is deployed:- https://sql-editor-with-vue.netlify.app/


Thank you, It was fun working on this task. Learnt a couple of new things as well :)