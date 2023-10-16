# Indexer tutorials

_Indexer_ is a powerful platform designed to streamline and simplify the process of indexing blockchain data, specifically tailored for non-fungible tokens (NFTs) across different blockchain networks. Here's a breakdown of its key features and how it can benefit users:

* **Historical data**: Indexer provides access to historical data related to NFTs, including ownership changes, metadata updates, rarity information, minting events, transfers, staking actions, and more. This historical data allows users to track the evolution of NFTs over time.
* **Real-time data**: The platform offers real-time synchronization of NFT data and blockchain data analytics, updating within seconds of each transaction. This ensures that users have access to the most current information about NFT events and activities.
* **Standardized and uniform data**: Indexer standardizes and uniformly formats data across various blockchain networks. This uniformity makes it easier for developers to implement the data into their applications without worrying about the complexities of dealing with different blockchain protocols.
* **Marketplace insights**: Indexer provides insights into various aspects of NFT marketplaces, such as listings, bids, buys, order books, collection offers, commissions, and royalties. This data can help users make informed decisions about their NFT investments and strategies.
* **NFT portfolio analytics**: For NFT collectors and investors, Indexer offers comprehensive portfolio data, including portfolio value, profit and loss analysis, historical value trends, fiat conversions, unrealized gains, and information about the best and worst trades. This data assists users in managing and optimizing their NFT portfolios.
* **NFT media hosting**: The platform hosts NFT media, such as images, videos, and audio files, on an IPFS (InterPlanetary File System) gateway. This ensures that users can access and retrieve NFT media files with ease, using URL query formatting for specific requests.
* **GraphQL Integration**: Indexer is built with GraphQL, a query language for APIs that allows users to retrieve precisely the data they need. This integration makes it efficient for developers and users to access NFT-related data and perform complex queries.
* **Use Cases**: The platform caters to a wide range of use cases. For example, NFT collectors can use Indexer to track their portfolio's performance, historical value trends, and individual NFT details. Developers can utilize Indexer's standardized data to build applications and services that require NFT-related information.

**Why use indexer?**

* **Efficiency**: Instead of spending months developing a custom NFT indexer for each blockchain network, developers can save time and effort by building on top of Indexer, which offers ready-to-use data and analytics.
* **Reliability**: Indexer ensures reliable and up-to-date data through its real-time synchronization, reducing the risk of data inaccuracies or outdated information.
* **Affordability**: The platform offers a cost-effective solution compared to the resources required to develop and maintain chain-specific indexers.
*   **Data Insights**: Indexer's aggregated data and analytics provide valuable insights into NFT markets, portfolios, and events, empowering users to make informed decisions.



In summary, Indexer is a comprehensive platform that centralizes NFT-related data and analytics, making it a valuable tool for both NFT collectors and developers seeking to build applications on top of blockchain networks. It offers standardized, real-time, and historical data across different blockchain networks, making it a reliable and efficient solution for indexing NFTs.

**Indexer.xyz query snippets**

1. Find an NFT compilation on a compatible blockchain and rephrase the description!\
   ![](<../.gitbook/assets/image (1).png>)
2. Select any button labeled as "Query Code" or bearing the GraphQL symbol.\
   ![](<../.gitbook/assets/image (2).png>)
3. View the precise query and variables utilized for crafting that element directly on the webpage.\
   ![](<../.gitbook/assets/image (3).png>)

**Wallet API**

The Indexer Wallet API is divided into 3 categories:

1. _wallet\_stats_ - Overall wallet performance
2. _wallet\_holdings_ - Unrealized performance of each NFT held by a wallet
3. _wallet\_trades_ - Realized performance on each NFT a wallet has sold

Examples can be found on a wallet page like [this one](https://indexer.xyz/aptos/0x8afb40b62a4f6db8f2a3dc0674ff01262acbe5714707101aee80d8af1e60a0df?tab=profit/loss) and queries tested for free in [the explorer](https://indexer.xyz/api-explorer) on indexer.xyz!

**React implementation**

We're going to create a React app that retrieves and shows information from the Aptos NFT marketplace. This data will be fetched using a GraphQL API offered by Indexer.xyz.

* Setting up the project:\
  Firstly, create a new React application using the create-react-app command:\
  `npx create-react-app market-data-visualization`
*   Next,we establish a fetchQuery function responsible for generating a GraphQL query designed to retrieve market data for the specified timeframe.



```tsx
const fetchQuery = (marketName) => {
    let timeframeCondition = '';
    if (TIME_FRAMES[timeframe]) {
      timeframeCondition = `}, block_time: {_gt: "${new Date(Date.now() - TIME_FRAMES[timeframe]).toISOString()}"}`;
    }

    const query = `
      query {
        sui {
          actions_aggregate(where: {type: {_eq: "buy"}, market_name: {_eq: "${marketName}"${timeframeCondition}}) {
            aggregate {
              sum {
                usd_price
              }
            }
          }
        }
      }
    `;

    return fetch('https://api.indexer.xyz/graphql', {
      method: 'POST',
      headers: {
        "x-api-user": "-YOUR-USERNAME-",
        "x-api-key": "-YOUR-API-KEY-",
        "apikey_type": "prod",
        "role": "prod-user",
        "host_name": "api.indexer.xyz",
        "Content-Type": "application/json",
      },
      body: JSON.stringify({ query }),
    })
    .then(response => response.json())
    .then(response => response.data);
  }
```

\


* Fetching and Displaying Data:\
  Afterwards, we employ the useEffect hook to fetch data from the API either when the component is initially rendered or when there's a modification in the chosen timeframe.

```tsx
useEffect(() => {
    setLoading(true);

    const marketNames = ['souffl3', 'bluemove', 'clutchy', 'tocen', 'keepsake'];

    Promise.all(marketNames.map(marketName => fetchQuery(marketName)))
      .then(responses => {
        setData(
          responses.map((response, index) => {
            return {
              market: marketNames[index],
              sum: response.sui.actions_aggregate.aggregate.sum.usd_price,
            };
          })
        );
        setLoading(false);
      })
      .catch(error => {
        setError(error);
        setLoading(false);
      });
  }, [timeframe]);
```

*   Wrapping Up With The App Component:

    Finally, we create the App component that renders our MyTable component.

```tsx
<div>
      <select value={timeframe} onChange={(e) => setTimeframe(e.target.value)}>
        {Object.keys(TIME_FRAMES).map((key) => (
          <option key={key} value={key}>{key}</option>
        ))}
      </select>

      <table>
        <thead>
          <tr>
            <th>Market Name</th>
            <th>Volume</th>
          </tr>
        </thead>
        <tbody>
          {sortedData.map((item, index) => (
            <tr key={index}>
              <td>{item.market}</td>
              <td>{Number(item.sum).toLocaleString('en-US', { style: 'currency', currency: 'USD', minimumFractionDigits: 0, maximumFractionDigits: 0 })}</td>
            </tr>
          ))}
          <tr>
            <td><strong>Total</strong></td>
            <td><strong>{sortedData.reduce((total, item) => total + item.sum, 0).toLocaleString('en-US', { style: 'currency', currency: 'USD', minimumFractionDigits: 0, maximumFractionDigits: 0 })}</strong></td>
          </tr>
        </tbody>
      </table>
      <p>Powered by   <a href="https://indexer.xyz"> <img src="https://indexer.xyz/indexer-logo.svg"></img></a></p>
    </div>

```

&#x20;

&#x20;



\














\
\


\


\


\




\


\


\


\


\


\


\


\
