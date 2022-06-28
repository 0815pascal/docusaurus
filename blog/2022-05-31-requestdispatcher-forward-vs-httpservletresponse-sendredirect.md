---
slug: requestdispatcher-forward-vs-httpservletresponse-sendredirect
title: RequestDispatcher.forward() vs HttpServletResponse.sendRedirect()
authors:
  name: Pascal
  title: Full Stack Web Dev
  url: https://github.com/0815pascal
  image_url: https://github.com/0815pascal.png
tags: [Java, Servlets, JSP, Redirect, Forward]
---

> What is the conceptual difference between `forward()` and `sendRedirect()`?

Source: [BalusC](https://stackoverflow.com/questions/2047122/requestdispatcher-forward-vs-httpservletresponse-sendredirect) In the web development world, the term "redirect" is the act of sending the client an empty HTTP response with just a `Location` header containing the new URL to which the client has to send a brand new GET request. So basically:

- Client sends a HTTP request to `some.jsp`.
- Server sends a HTTP response back with `Location: other.jsp` header
- Client sends a HTTP request to `other.jsp` (this get reflected in browser address bar!)
- Server sends a HTTP response back with content of `other.jsp`.

You can track it with the web browser's builtin/addon developer toolset. Press F12 in Chrome/IE9/Firebug and check the "Network" section to see it.

Exactly the above is achieved by `sendRedirect("other.jsp")`. The `RequestDispatcher#forward()` doesn't send a redirect. Instead, it uses the content of the target page as HTTP response.

- Client sends a HTTP request to `some.jsp`.
- Server sends a HTTP response back with content of `other.jsp`.

However, as the original HTTP request was to `some.jsp`, the URL in browser address bar remains unchanged. Also, any request attributes set in the controller behind `some.jsp` will be available in `other.jsp`. This does not happen during a redirect because you're basically forcing the client to create a **new** HTTP request on `other.jsp`, hereby throwing away the original request on `some.jsp` including all of its attribtues

----

The `RequestDispatcher` is extremely useful in the MVC paradigm and/or when you want to hide JSP's from direct access. You can put JSP's in the `/WEB-INF` folder and use a `Servlet` which controls, preprocesses and postprocesses the requests. The JSPs in the `/WEB-INF` folder are not directly accessible by URL, but the Servlet can access them using `RequestDispatcher#forward()`.

You can for example have a JSP file in `/WEB-INF/login.jsp` and a `LoginServlet` which is mapped on an `url-pattern` of `/login`. When you invoke `http://example.com/context/login`, then the servlet's `doGet()` will be invoked. You can do any *pre*processing stuff in there and finally **forward** the request like:

```Java
request.getRequestDispatcher("/WEB-INF/login.jsp").forward(request, response);
```

When you submit a form, you normally want to use `POST`:

```html
<form action="login" method="post">
```

This way the servlet's `doPost()` will be invoked and you can do any postprocessing stuff in there (e.g. validation, business logic, login the user, etc).

If there are any errors, then you normally want to **forward** the request back to the same page and display the errors there next to the input fields and so on. You can use the `RequestDispatcher` for this.

If a `POST` is successful, you normally want to **redirect** the request, so that the request won't be resubmitted when the user refreshes the request (e.g. pressing F5 or navigating back in history).

```java
User user = userDAO.find(username, password);
if (user != null) {
    request.getSession().setAttribute("user", user); // Login user.
    response.sendRedirect("home"); // Redirects to <http://example.com/context/home> after succesful login.
} else {
    request.setAttribute("error", "Unknown login, please try again."); // Set error.
    request.getRequestDispatcher("/WEB-INF/login.jsp").forward(request, response); // Forward to same page so that you can display error.
}
```

A **redirect** thus instructs the client to fire a new `GET` request on the given URL. Refreshing the request would then only refresh the redirected request and not the initial request. This will avoid "double submits" and confusion and bad user experiences. This is also called the [POST-Redirect-GET pattern](http://en.wikipedia.org/wiki/Post/Redirect/Get).

----

### requestDispatcher - forward() method

1. When we use the `forward` method, the request is transferred to another resource within the same server for further processing.

2. In the case of `forward`, the web container handles all processing internally and the client or browser is not involved.

3. When `forward` is called on the `requestDispatcherobject`, we pass the request and response objects, so our old request object is present on the new resource which is going to process our request.

4. Visually, we are not able to see the forwarded address, it is transparent.

5. Using the `forward()` method is faster than `sendRedirect`.

6. When we redirect using forward, and we want to use the same data in a new resource, we can use `request.setAttribute()` as we have a request object available.

### SendRedirect

1. In case of `sendRedirect`, the request is transferred to another resource, to a different domain, or to a different server for further processing.

2. When you use `sendRedirect`, the container transfers the request to the client or browser, so the URL given inside the `sendRedirect` method is visible as a new request to the client.

3. In case of `sendRedirect` call, the old request and response objects are lost because it’s treated as new request by the browser.

4. In the address bar, we are able to see the new redirected address. It’s not transparent.

5. `sendRedirect` is slower because one extra round trip is required, because a completely new request is created and the old request object is lost. Two browser request are required.

6. But in `sendRedirect`, if we want to use the same data for a new resource we have to store the data in session or pass along with the URL.

### Which one is good?

Its depends upon the scenario for which method is more useful.

If you want control is transfer to new server or context, and it is treated as completely new task, then we go for `sendRedirect`. Generally, a forward should be used if the operation can be safely repeated upon a browser reload of the web page and will not affect the result.

Source: [Abhijeet Ashok Muneshwar](https://stackoverflow.com/a/19289165/4146792) / [Javarevisited](https://javarevisited.blogspot.com/2011/09/sendredirect-forward-jsp-servlet.html)

### See also

- [How do servlets work? Instantiation, sessions, shared variables and multithreading](https://stackoverflow.com/questions/3106452/how-do-servlets-work-instantiation-sessions-shared-variables-and-multithreadi)
- [doGet and doPost in Servlets](https://stackoverflow.com/questions/2349633/doget-and-dopost-in-servlets)
- [How perform validation and display error message in same form in JSP?](https://stackoverflow.com/questions/6464931/how-perform-validation-and-display-error-message-in-same-form-in-jsp)
- [HttpServletResponse sendRedirect permanent](https://stackoverflow.com/questions/9034149/httpservletresponse-sendredirect-permanent)
