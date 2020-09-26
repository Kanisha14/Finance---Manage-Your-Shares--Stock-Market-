# Finance - Manage Your Shares (Stock Market)
As a part of CS59, Implementing a website via which users can “buy” and “sell” stocks.

## Prototype Given:
<https://finance.cs50.net/>

## Framework & Languages
- HTML, CSS, JavaScript, Bootstrap
- Flask, Python
- SQL Database

## Configuring
Before getting started, we’ll need to register for an API key in order to be able to query IEX’s data. To do so, follow these steps:

- Visit iexcloud.io/cloud-login#/register/.
- Enter your email address and a password, and click “Create account”.
- On the next page, scroll down to choose the Start (free) plan.
- Once you’ve confirmed your account via a confirmation email, sign in to iexcloud.io.
- Click API Tokens.
- Copy the key that appears under the Token column (it should begin with pk_).
- In a terminal window within CS50 IDE, execute:
  $ export API_KEY=value
Note: where value is that (pasted) value, without any space immediately before or after the =. You also may wish to paste that value in a text document somewhere, in case you need it again later.

## Specification
 1. ### register ### 
 
 Complete the implementation of register in such a way that it allows a user to register for an account  via a form
  - Require that a user input a username, implemented as a text field whose name is username. Render an     apology if the user’s input is blank or the username already exists.
  - Require that a user input a password, implemented as a text field whose name is password, and then that same password again, implemented as a text field whose name is confirmation. Render an apology if either input is blank or the passwords do not match.
  - Submit the user’s input via POST to /register.
  - INSERT the new user into users, storing a hash of the user’s password, not the password itself. Hash the user’s password with generate_password_hash Odds are you’ll want to create a new template (e.g., register.html) that’s quite similar to login.html.

Once you’ve implemented register correctly, you should be able to register for an account and log in (since login and logout already work)! And you should be able to see your rows via phpLiteAdmin or sqlite3.

2. ### quote ###

Complete the implementation of quote in such a way that it allows a user to look up a stock’s current price.
   - Require that a user input a stock’s symbol, implemented as a text field whose name is symbol.
   - Submit the user’s input via POST to /quote.
   - Odds are you’ll want to create two new templates (e.g., quote.html and quoted.html). When a user visits /quote via GET, render one of those templates, inside of which should be an HTML form that submits to /quote via POST. In response to a POST, quote can render that second template, embedding within it one or more values from lookup.
   
3. ### buy ###
 
 Complete the implementation of buy in such a way that it enables a user to buy stocks.

   - Require that a user input a stock’s symbol, implemented as a text field whose name is symbol. Render an apology if the input is blank or the symbol does not exist (as per the return value of lookup).
   - Require that a user input a number of shares, implemented as a text field whose name is shares. Render an apology if the input is not a positive integer.
   - Submit the user’s input via POST to /buy.
   - Odds are you’ll want to call lookup to look up a stock’s current price.
   - Odds are you’ll want to SELECT how much cash the user currently has in users.
   - Add one or more new tables to finance.db via which to keep track of the purchase. Store enough information so that you know who bought what at what price and when.
     - Use appropriate SQLite types.
     - Define UNIQUE indexes on any fields that should be unique.
     - Define (non-UNIQUE) indexes on any fields via which you will search (as via SELECT with WHERE).
   - Render an apology, without completing a purchase, if the user cannot afford the number of shares at the current price.
   - You don’t need to worry about race conditions (or use transactions).

Once you’ve implemented buy correctly, you should be able to see users’ purchases in your new table(s) via phpLiteAdmin or sqlite3.

4. ### index ###
 Complete the implementation of index in such a way that it displays an HTML table summarizing, for the user currently logged in, which stocks the user owns, the numbers of shares owned, the current price of each stock, and the total value of each holding (i.e., shares times price). Also display the user’s current cash balance along with a grand total (i.e., stocks’ total value plus cash).

   - Odds are you’ll want to execute multiple SELECTs. Depending on how you implement your table(s), you might find GROUP BY HAVING SUM and/or WHERE of interest.
   - Odds are you’ll want to call lookup for each stock.
   
5. ### sell ###
 Complete the implementation of sell in such a way that it enables a user to sell shares of a stock (that he or she owns).

   - Require that a user input a stock’s symbol, implemented as a select menu whose name is symbol. Render an apology if the user fails to select a stock or if (somehow, once submitted) the user does not own any shares of that stock.
   - Require that a user input a number of shares, implemented as a text field whose name is shares. Render an apology if the input is not a positive integer or if the user does not own that many shares of the stock.
   - Submit the user’s input via POST to /sell.
   - You don’t need to worry about race conditions (or use transactions).

6. ### history ###
 Complete the implementation of history in such a way that it displays an HTML table summarizing all of a user’s transactions ever, listing row by row each and every buy and every sell.

   - For each row, make clear whether a stock was bought or sold and include the stock’s symbol, the (purchase or sale) price, the number of shares bought or sold, and the date and time at which the transaction occurred.
   - You might need to alter the table you created for buy or supplement it with an additional table. Try to minimize redundancies.
 
## Background
If you’re not quite sure what it means to buy and sell stocks (i.e., shares of a company), head here for a tutorial.

You’re about to implement C$50 Finance, a web app via which you can manage portfolios of stocks. Not only will this tool allow you to check real stocks’ actual prices and portfolios’ values, it will also let you buy (okay, “buy”) and sell (okay, “sell”) stocks by querying IEX for stocks’ prices.

Indeed, IEX lets you download stock quotes via their API (application programming interface) using URLs like https://cloud-sse.iexapis.com/stable/stock/nflx/quote?token=API_KEY. Notice how Netflix’s symbol (NFLX) is embedded in this URL; that’s how IEX knows whose data to return. That link won’t actually return any data because IEX requires you to use an API key (more about that in a bit), but if it did, you’d see a response in JSON (JavaScript Object Notation) format like this:

`{  
   "symbol": "NFLX",
  
  "companyName": "Netflix, Inc.",
   
   "primaryExchange": "NASDAQ",
   
   "calculationPrice": "close",
   
   "open": 317.49,
   
   "openTime": 1564752600327,
   
   "close": 318.83,
   
   "closeTime": 1564776000616,
   
   "high": 319.41,
   
   "low": 311.8,
   
   "latestPrice": 318.83,
   
   "latestSource": "Close",
   
   "latestTime": "August 2, 2019",
   
   "latestUpdate": 1564776000616,
   
   "latestVolume": 6232279,
   
   "iexRealtimePrice": null,
   
   "iexRealtimeSize": null,
   
   "iexLastUpdated": null,
   
   "delayedPrice": 318.83,
   
   "delayedPriceTime": 1564776000616,
   
   "extendedPrice": 319.37,
   
   "extendedChange": 0.54,
   
   "extendedChangePercent": 0.00169,
   
   "extendedPriceTime": 1564876784244,
   
   "previousClose": 319.5,
   
   "previousVolume": 6563156,
   
   "change": -0.67,
   
   "changePercent": -0.0021,
   
   "volume": 6232279,
   
   "iexMarketPercent": null,
   
   "iexVolume": null,
   
   "avgTotalVolume": 7998833,
   
   "iexBidPrice": null,
   
   "iexBidSize": null,
   
   "iexAskPrice": null,
   
   "iexAskSize": null,
   
   "marketCap": 139594933050,
   
   "peRatio": 120.77,
   
   "week52High": 386.79,
   
   "week52Low": 231.23,
   
   "ytdChange": 0.18907500000000002,
   
   "lastTradeTime": 1564776000616
}`

Notice how, between the curly braces, there’s a comma-separated list of key-value pairs, with a colon separating each key from its value.
