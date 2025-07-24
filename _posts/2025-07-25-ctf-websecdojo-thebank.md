# WebSecDojo.com - The Bank
## My first post :)
### Prerequisites
<ul>
  <li>Basic knowledge of HTTP requests and API endpoints.</li>
  <li>Developer Panel (I recommend using firefox) or similar software that enables sending web requests.</li>
</ul>
### Exploring the website
<p>
  After entering the challenge I was in `https://websecdojo.com/thebank/index.php`.<br/>
  Since I didn't have an account I thought it'd be better to attempt to create one so I clicked `Don't have an account? Register here`.
</p>
### Trying to register
<p>
  First because I know it's a CTF challenge focused on HTTP requests I opened the network tab.<br/>
  Entered fabricated data and submitted the web-form.
  Found a `POST` request to this URL: `https://websecdojo.com/thebank/register.php`.
  The request's had a parameter named `is_admin` the was set to `0`. When changed to `1`, resending the request will result in a `GET` request to the URL: `https://websecdojo.com/thebank/dashboard.php`.
</p>
### The dashboard
<p>
  
</p>
