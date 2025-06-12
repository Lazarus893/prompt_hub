# API Documentation

## SyncFetch

### Parameters

| Field   | Type   | Description                 |
| ------- | ------ | --------------------------- |
| url     | string | url to fetch.               |
| method  | string | http method, e.g. GET,POST. |
| headers | object |                             |
| body    | string |                             |

### Return type

`$FetchResponse`

### Example Code

```jsx
const {syncFetch: fetch} = require("net/http");

const r = fetch('https://httpbin.org/post', {
    method: 'POST',
    headers: {
        'Content-Type': 'application/json'
    },
    body: JSON.stringify({
        message: 'Hello from browser',
        time: Date.now()
    })
})

log('json:\n', JSON.stringify(r.json(), null, 2))
log('text:\n', r.text())
log('status:\n', r.status)
log('headers:\n', r.headers)
```

## GetTimeline

Retrieves temporally ordered events and discussions from news and Twitter based on a user intent, enabling semantic timeline search for concise, context-rich insights.

If user provide a list of keywords, you should analysis and group them, for each group you need to extract an intent as the query param. You need to make seperate request for each group.

The intent must be written as a concise noun phrase (max 8 words) and extracted from user’s request, avoiding verbs and expressing a clear topic, entity, or issue that can be tracked over time.

You can also use the exact matching of keywords to query, please note that the two query methods of intents and keywords are mutually exclusive, and you can only use one of them in a query. You should use `Intents` when you want to query for very specific events, such as (Trump's views on the tariff war between China and the United States), and when you want to query a relatively broad concept, such as (BTC policy), you should use `keywords`.

### Parameters

| Field           | Type     | Description                                                                                                                                                                                                                                                                                                                          |
| --------------- | -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `intents`       | []string | Intents can be inferred from users' keywords or stated intents.                                                                                                                                                                                                                                                                      |
| `category_list` | []string | Category is optional and should only be used when the user shows clear interest in specific categories. Available categories: PRICE_MOVEMENT, NETWORK_LAUNCH, MACRO_TRENDS, WHALE_MOVES, LISTING_ANNOUNCEMENT, FUND_FLOWS, AIRDROP_INFO, TECH_DEVELOPMENT, PARTNERSHIP, METRICS_MILESTONE, RESEARCH_INSIGHT, ETF_FLOWS, GENERAL_NEWS |
| `start`         | int      | Start time of the query range (unix timestamp)                                                                                                                                                                                                                                                                                       |
| `end`           | int      | End time of the query range (unix timestamp)                                                                                                                                                                                                                                                                                         |

### Return Type

`[]$TimelineEvent.`

Returned content are all in English(en-US).

### Example Code

```jsx
const access = require('access')
const result = access.getTimeline({
    intents: ['bitcoin price movement, news or any events'],
    start: 1744643995,
    end: 1747235995
})
const event0 = result[0]
```

## Embed

Get embedding vector of texts via openai embedding model.

### Parameters

| Field       | Type     | Description                                                                   |
| ----------- | -------- | ----------------------------------------------------------------------------- |
| `text_list` | []string | array of strings to calculate embeddings.make sure elements are valid string. |

### Return Type

array of []float.

### Example Code

```jsx
const misc = require('misc')
const result = misc.embed(
    {
        text_list: ['hello alva!', '你好，Alva！'],
    }
);
const vector1 = result[0] // embedding of text_list[0]
const vector2 = result[1] // embedding of text_list[1]
```

## Translate

Translates text between multiple languages while preserving meaning, tone, and context. Supports accurate bidirectional translation for both formal and informal content.

### Parameters

| Field                                                                                     | Type     | Description                                                                   |
| ----------------------------------------------------------------------------------------- | -------- | ----------------------------------------------------------------------------- |
| `text_list`                                                                               | []string | array of strings to calculate translate. make sure elements are valid string. |
| `lang`                                                                                    | string   | target language, support:                                                     |
| `'en-US','zh-CN', 'zh-HK', 'ja-JP', 'id-ID', 'vi-VN', 'ko-KR', 'ru-RU', 'fr-FR', 'tr-TR'` |

### Return Type

[]string

### Example Code

```jsx
const misc = require('misc')
const result = misc.translate(
    {
        lang:'zh-CN',
        text_list: ['hello','world'],
    }
);
const text0 = result[0] // translation of text_list[0]
const text1 = result[1] // translation of text_list[1]
```

## TextMatch

Calculate the similarity of 2 texts.

### Parameters

| Field       | Type    | Description                                                                                                                                                                                         |
| ----------- | ------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `algo`      | string  | `text` means string match, either `text1` containing `text2` or `text2` containing `text1` indicates a match.<br>`cosine` means using cosine similarity algorithm to check `vector1` and `vector2`. |
| `threshold` | string  | stringfy float, similarity being bigger than the `threshold` means match, used when `algo` is `cosine`                                                                                              |
| `text1`     | string  | used when `algo` is `text`                                                                                                                                                                          |
| `text2`     | string  | used when `algo` is `text`                                                                                                                                                                          |
| `vector1`   | []float | used when `algo` is `cosine`, can be calculated via `Embed`                                                                                                                                         |
| `vector2`   | []float | used when `algo` is `cosine`,can be calculated via `Embed`                                                                                                                                          |

### Return Type

```jsx
{
	match: boolean,
	score: float
}
```

### Example Code

example1:

```jsx
const misc = require('misc')
const result = misc.embed(
    {
        text_list: ['hello alva!', '你好，Alva！'],
    }
);
const vector1 = result[0]
const vector2 = result[1]

const similarity = misc.textMatch({
    algo: 'cosine',
    threshold: '0.92',
    vector1: vector1,
    vector2: vector2
})
log(similarity.score, similarity.match)

```

example2:

```jsx
const misc = require('misc')
const similarity = misc.textMatch({
    algo: 'text',
    text1: 'hello world',
    text2: 'hello'
})
log(similarity.score, similarity.match)
```

## GetTransactions

List of wallets to query transactions for. Each wallet must explicitly specify its chain information. If the user does not provide chain information, it can be inferred from the address format. If an address starts with "0x", it is likely an EVM address, and the user should be prompted to provide explicit chain information. For Solana addresses, the chain information will be automatically supplemented, and the query will proceed.If the user does not provide any addresses, the tool can optionally fall back to Alva’s curated smart money address list by setting `enable_official_address_list` to true.

### Parameters

| Field                          | Type      | Description                                                                           |
| ------------------------------ | --------- | ------------------------------------------------------------------------------------- |
| `enable_official_address_list` | boolean   | Whether to include official address list in the query, set false if user not specify. |
| `wallet_list`                  | []$Wallet | List of wallet to query transactions for                                              |
| `start`                        | int       | Start time of the query range (unix timestamp)                                        |
| `end`                          | int       | End time of the query range (unix timestamp)                                          |

### Return Type

`[]$Tx` 

### Example Code

```jsx
const access = require('access')
// Call the API
const result = access.getTransactions({
  enable_official_address_list: false,
  wallet_list: [
        {
            address: '',
            network: 'solana',
        },
        {
            address: '',
            network: 'bsc',
        }
    ],
  start: 1646092800,
  end: 1646179200
});

for (const tx of result) {
  log(`Transaction: ${tx.wallet_addr} ${tx.dir} ${tx.amount} ${tx.asset} ${tx.amount_usd}`);
}

```

## GetProject

Retrieves detailed project information using a contract address or ticker symbol. Returns key metadata including project name, network, logos, price, market cap, and 24h trading volume to support quick identification and contextual understanding of blockchain assets.

### Parameters

| Field     | Type   | Description                                |
| --------- | ------ | ------------------------------------------ |
| `ca`      | string | Contract address of the project (optional) |
| `symbol`  | string | Ticker symbol of the project (optional)    |
| `network` | string | Network of the project/token (optional)    |

### Return Type

`$Project` 

### Example Code

```jsx
const access = require('access')

const result = access.getProject({
  symbol: 'BTC', ca:'', network: 'solana'
});
const project = result;
log(`Project: ${project.name} (${project.symbol}) - $${project.price}`);

```

```jsx
const access = require('access')

const result = access.getProject({
  ca: '0x123', symbol:'', network: 'bsc'
});
const project = result;
log(`Project: ${project.name} (${project.symbol}) - $${project.price}`);

```

## GetSmartSignal

Retrieves smart signals for a specific crypto project within a given time range, covering a wide variety of market, behavioral, and event-based insights. Signal types include market anomalies (e.g., price, volume, funding rate), smart money activities (e.g., whale, KOL, VC moves), community and sentiment trends (e.g., social heat, sentiment), tokenomics (e.g., unlocks, airdrops), and key events (e.g., announcements, DAO proposals, flash news). Content can be localized by language.

### Parameters

| Field               | Type     | Description                                    |
| ------------------- | -------- | ---------------------------------------------- |
| `smart_signal_list` | string[] | List of signal types to retrieve               |
| `project_id`        | int64    | Unique identifier of the project               |
| `language`          | string   | Language code for the signal content           |
| `start`             | int      | Start time of the query range (unix timestamp) |
| `end`               | int      | End time of the query range (unix timestamp)   |

### Return Type

`[]$SmartSignal` 

### Example Code

```jsx
const access = require('access')

const result = access.getSmartSignal({
  smart_signal_list: ['PRICE_ANOMALY', 'SOCIAL_HEAT'],
  project_id: 123,
  language: 'en-US',
  start: 1646092800,
  end: 1646179200
});
const signals = result;
for (const signal of signals) {
  log(`Signal: ${signal.kind} - ${signal.content}`);
}

```

## GetXFollowers

Tracks how the follower counts of selected KOLs change over time, useful for analyzing influence, reach, and community engagement.

### Parameters

| Field      | Type     | Description                                    |
| ---------- | -------- | ---------------------------------------------- |
| `kol_list` | string[] | List of KOL usernames to query followers for   |
| `start`    | int      | Start time of the query range (unix timestamp) |
| `end`      | int      | End time of the query range (unix timestamp)   |

### Return Type

`[]$TwitterUser` 

### Example Code

```jsx
const access = require('access')

const result = access.getXFollowers( {
  kol_list: ['elonmusk', 'vitalikbuterin'],
  start: 1646092800,
  end: 1646179200
});
const followers = result;
for (const user of followers) {
  log(`Follower: ${user.username} (${user.followers_count} followers)`);
}

```

## GetXFollowings

Fetches accounts that selected KOLs started following during a specified time window.

### Parameters

| Field      | Type     | Description                                    |
| ---------- | -------- | ---------------------------------------------- |
| `kol_list` | string[] | List of KOL usernames to query followings for  |
| `start`    | int      | Start time of the query range (unix timestamp) |
| `end`      | int      | End time of the query range (unix timestamp)   |

### Return Type

`[]$TwitterUser` 

### Example Code

```jsx
const access = require('access')

const result = access.getXFollowings({
  kol_list: ['elonmusk', 'vitalikbuterin'],
  start: 1646092800,
  end: 1646179200
});
const followings = result;
for (const user of followings) {
  log(`Following: ${user.username} (${user.followers_count} followers)`);
}

```

## GetXPostsByUsername

Fetches KOL-generated posts  in the recent period for content and sentiment analysis.

### Parameters

| Field      | Type     | Description                                    |
| ---------- | -------- | ---------------------------------------------- |
| `kol_list` | string[] | List of KOL usernames to query posts for       |
| `start`    | int      | Start time of the query range (unix timestamp) |

### Return Type

`[]$XPost` 

### Example Code

```jsx
const access = require('access')

const result = access.getXPostsByUsername({
  kol_list: ['elonmusk', 'vitalikbuterin'],
  start: 1646092800
});
const posts = result;
for (const post of posts) {
  log(`Post: ${post.author.username} - ${post.content.substring(0, 100)}...`);
}
```

## GetXUserByName

Looks up a Twitter account by username and returns public profile data.

### Parameters

| Field      | Type   | Description                             |
| ---------- | ------ | --------------------------------------- |
| `username` | string | Twitter username to look up (without @) |

### Return Type

`$TwitterUser` 

### Example Code

```jsx
const access = require('access')

const result = access.getXUserByName({
  username: 'elonmusk'
});
const user = result;
log(`User: ${user.name} (@${user.username}) - ${user.followers_count} followers`);

```

## GetMarketData

Get market prices and metrics with time range and period.

### Parameters

| Field    | Type            | Description                                                                                                                   |
| -------- | --------------- | ----------------------------------------------------------------------------------------------------------------------------- |
| `label`  | MarketDataLabel | refer to Available Data                                                                                                       |
| `symbol` | string          | The symbol ticker of the project, when query price, its format is BINANCE_SPOT_{SYMBOL}_USDT, when other labels, use {SYMBOL} |
| `start`  | int             | Start timestamp of the data range (unix)                                                                                      |
| `end`    | int             | End timestamp of the data range (unix)                                                                                        |
| `period` | string          | Resolution of the data points (e.g., '1h', '1d')                                                                              |

### Available Data

| Description                                                                             | Label                              | Notes                                                                                                                                                                            |
| --------------------------------------------------------------------------------------- | ---------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| price, ohlcv of token                                                                   | `PRICE`                            |                                                                                                                                                                                  |
| funding rate of token                                                                   | `FUNDING_RATE`                     |                                                                                                                                                                                  |
| open interest of token                                                                  | `OPEN_INTEREST`                    |                                                                                                                                                                                  |
| inflow of BTC into exchange wallets                                                     | `EXCHANGE_INFLOW`                  | only support symbol “BTC” and period “1d”                                                                                                                                        |
| The difference between coins flowing into exchanges and flowing out of exchanges        | `EXCHANGE_NETFLOW`                 | only support symbol “BTC” and period “1d”                                                                                                                                        |
| SOPR Ratio is calculated as long term holders' SOPR divided by short term holders' SOPR | `SOPR_RATIO`                       | only support symbol “BTC” and period “1d”                                                                                                                                        |
| total number of unique addresses that were active on the blockchain                     | `ADDRESS_COUNT`                    | only support symbol “BTC” and period “1d”                                                                                                                                        |
| The difference between coins flowing into mining pools and flowing out of mining pools  | `MINER_NETFLOW`                    | only support symbol “BTC” and period “1d”                                                                                                                                        |
| the amount of stablecoin tokens in existence                                            | `STABLECOIN_SUPPLY`                | only support symbol “USDT” and “USDC” and period “1d”                                                                                                                            |
| yahoo finance metrics                                                                   | `YAHOO_FINANCE`                    | use one of these symbols: TNX (10-year US Treasury Rate), GOLD (Gold Futures), NDX (NASDAQ 100 Index), NQF (NASDAQ 100 Futures), DXY (US Dollar Index); only support period “1d” |
| liquidation amount of crypto token in a time range                                      | `COIN_LIQUIDATION_HISTORY`         | the data are from central exchange ‘binance’                                                                                                                                     |
| taker buy/sell amount of crypto future token in a time range                            | `COIN_TAKER_BUYSELL_VOLUME_FUTURE` | the data are from central exchange ‘binance’                                                                                                                                     |
| funding rate with interval data                                                         | `FUNDING_RATE_WITH_INTERVAL`       | returns funding rate and interval hours from central exchange ‘binance’                                                                                                          |

### Return Type

For `PRICE` label: `[]$Bar` 

For `COIN_LIQUIDATION_HISTORY`: `$LiquiditionData`

For `COIN_TAKER_BUYSELL_VOLUME_FUTURE`: `$VolumeData`

For `FUNDING_RATE_WITH_INTERVAL`: `$FundingRateWithIntervalData`
For other labels:  `[]$TsData` : 

### Example Code

```jsx
const access = require('access')

const result = access.getMarketData({
  label: 'PRICE',
  symbol: 'BINANCE_SPOT_BTC_USDT',
  start: 1646092800,
  end: 1646179200,
  period: '1h'
});
const data = result
for (const bar of data) {
  log(`Price: ${bar.date} - $${bar.close}`);
}

```

```jsx
const access = require('access')

const result = access.getMarketData({
  label: 'COIN_LIQUIDATION_HISTORY', // BTC爆仓/强平量查询
  symbol: 'BTC',
  start: 1646092800,
  end: 1646179200,
});
```

```jsx
const access = require('access')

const result = access.getMarketData({
  label: 'COIN_TAKER_BUYSELL_VOLUME_FUTURE', // BTC合约买/卖量查询
  symbol: 'BTC',
  start: 1646092800,
  end: 1646179200,
});
```

```jsx
const access = require('access')

const result = access.getMarketData({
  label: 'FUNDING_RATE_WITH_INTERVAL', // 资金费率与费率间隔查询
  symbol: 'BTC',
  start: 1646092800,
  end: 1646179200,
});
// result contains: { funding_rate: 0.0001, funding_rate_interval: 8 }
log(`Funding Rate: ${result.funding_rate} - Interval: ${result.funding_rate_interval}h`);
```

## GetNewlyCreatedMemeTokens

Get newly created meme tokens on [pump.fun](http://pump.fun) and four.meme within a time period.

### Parameters

| Field     | Type   | Description                                              |
| --------- | ------ | -------------------------------------------------------- |
| `network` | string | Specify the chain which the token to be queried belongs. |
Only supported：solana/bsc.
If empty, query all chains. |
| `start` | int | Start time of the query range (unix timestamp) |
| `end` | int | End time of the query range (unix timestamp) |

### Return Type

`[]$MemeToken` 

### Example Code

```jsx
const access = require('access')

const result = access.getNewlyCreatedMemeTokens({
  network: 'solana',
  start: 1646092800,
  end: 1646179200
});
const tokens = result
for (const token of tokens) {
  log(`Token: ${token.name} (${token.symbol}) - ${token.network}`);
}
```

## CheckMemeTokenCreator

Get creator info of the meme token.

### Parameters

| Field              | Type   | Description                                    |
| ------------------ | ------ | ---------------------------------------------- |
| `twitter_username` | string | Twitter username of the potential creator      |
| `ca`               | string | Contract address of the token                  |
| `token_name`       | string | Name of the token                              |
| `token_symbol`     | string | Symbol of the token                            |
| `start`            | int    | Start time of the query range (unix timestamp) |
| `end`              | int    | End time of the query range (unix timestamp)   |

### Return Type

`$CheckMemeTokenCreatorResponse` 

### Example Code

```jsx
const access = require('access')

const result = access.checkMemeTokenCreator({
  twitter_username: 'elonmusk',
  ca: '0x123...',
  token_name: 'DogeCoin',
  token_symbol: 'DOGE',
  start: 1646092800,
  end: 1646179200
});
const check = result
log(`Creator Check: ${check.self_created} - ${check.message}`);

```

## GetWallets

Get wallets information (identity, labels, etc) for specific wallet addresses.

Or get wallet addresses for specific kind, labels or twitter.
Request parameters must be at least one.
You can do the wallet search based on user’s intention. For example, if a user wants a list of profitable wallets, you can search wallets under the kind of “SMART_MONEY” or wallets with label “10x Degen” and “100x Degen”. 

### Parameters

| Field       | Type     | Description                                         |
| ----------- | -------- | --------------------------------------------------- |
| `addresses` | []string | Specify addresses you want to query (optional)      |
| `kind`      | string   | Wallet kind, only support: KOL/VC/WHALE/SMART_MONEY |
| (optional)  |
| `label`     | string   | Wallet label (optional)                             |
- rule based label: 10x Degen, 100x Degen
- other customised labels |
| `twitter` | string | Query wallets by twitter name (optional) |

### Return Type

`[]$Wallet`

### Example Code

```jsx
const access = require('access')

const wallets = access.getWallets({
  kind: 'KOL',
});
const wallets = result
log(`Wallets: ${wallets?wallets.length:0}`);
```

## GetAgentMessages

Get the messages produced by a agent.

### Parameters

| Field                                                   | Type   | Description                                                                                       |
| ------------------------------------------------------- | ------ | ------------------------------------------------------------------------------------------------- |
| `agent_id`                                              | int    | Agent id of the query                                                                             |
| `direction`                                             | string | Query direction, only support: backward/forward                                                   |
| `cursor`                                                | int    | Cursor is the pagination query marker point, used to identify the starting position of the query. |
| If cursor = 0, default to querying the latest messages. |
| `limit`                                                 | int    | Limit of the query                                                                                |

### Return Type

`[]$Message`

### Example Code

```sql
const access = require('access')

const result = access.getAgentMessages({
  agent_id: 1,
  direction: 'backward',
  cursor: 0,
  limit: 100
});
const messages = result
log(`Agent Messages: ${messages?messages.length:0}`);
```

## GetKolsByPriority

Retrieve a list of KOLs (Key Opinion Leaders) based on their priority level.

### Parameters

| Field      | Type  | Description                                         |
| ---------- | ----- | --------------------------------------------------- |
| `priority` | int32 | Priority level of KOLs (0: High, 1: Medium, 2: Low) |

### Return Type

`[]$TwitterUser` 

### Example Code

```sql
const access = require('access')

const result = access.getKolsByPriority({
    priority: 1  // Get medium priority KOLs
});
const kols = result;
for (const kol of kols) {
    log(`KOL: ${kol.username} (${kol.followers_count} followers)`);
}
```

## GetMemeCreator

Get meme creator info with history created tokens by specified address and network.

### Parameters

| Field     | Type   | Description                  |
| --------- | ------ | ---------------------------- |
| `address` | string | Creator address of the query |
| `network` | string | Creator network of the query |

### Return Type

`$MemeCreator`

### Example Code

```sql
const access = require('access')

const result = access.getMemeCreator({
    address: "",
    network: "solana"
});
for (const token of result.created_tokens) {
		log(`Token: ${token.name} | ${token.symbol} | ${token.ca}`);
}
```

## SearchTweets

Search tweets by a concise query.

### Parameters

| Field   | Type   | Description                                      |
| ------- | ------ | ------------------------------------------------ |
| `query` | string | a short search query, e.g. "bitcoin recent news" |

### Return Type

`$Document`

## QueryKnowledgeBase

Query the knowledge base for a specific topic, optionally within a defined time range when there is a clear temporal intent. Return the most relevant document from sources such as news, social media, or wikis.

Only use publish time and event time fields when the query has a clear time-based intent. Leave them empty if not applicable:
- Use publish time fields to filter outdated content:
  - `publishTimeStart`: when to start searching for published content
  - `publishTimeEnd`: must be ≤ current time
  - Example: For "recent news on $BTC", set `publishTimeStart` to 7 days ago, `publishTimeEnd` to now
  - Example: For "latest social updates on $ETH from last month", set `publishTimeStart` to the start of last month and `publishTimeEnd` to the end of last month
- Use event time fields for past or future event-specific queries:
  - `eventTimeStart`: when the event or period starts
  - `eventTimeEnd`: when it ends
  - Example: For "token price prediction for next week", set `eventTimeStart` and `eventTimeEnd` to next week
  - Example: For "upcoming events for $ETH", set `eventTimeStart` to now, `eventTimeEnd` to 30 days from now
- Use both if needed:
  - Example: For "recent news on $BTC with price predictions for next month", set `publishTimeStart` to 7 days ago, `publishTimeEnd` to now, and `eventTimeStart` / `eventTimeEnd` to next month

### Parameters

| Field              | Type   | Description                                                                                                                                                    |
| ------------------ | ------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `query`            | string | a search query, e.g. "What are the goals and potential impact of the HR 3798 bill proposed in the US House to establish a national strategic Bitcoin reserve?" |
| `publishTimeStart` | int    | start time of the publish time range (unix timestamp)                                                                                                          |
| `publishTimeEnd`   | int    | end time of the publish time range (unix timestamp)                                                                                                            |
| `eventTimeStart`   | int    | start time of the event time range (unix timestamp)                                                                                                            |
| `eventTimeEnd`     | int    | end time of the event time range (unix timestamp)                                                                                                              |

### Return Type

`$Document`

## ChatCompletion

Invokes a large language model to handle diverse user tasks such as question answering, text generation, summarization and information extraction, etc.

### Parameters

| Field            | Type   | Description                      |
| ---------------- | ------ | -------------------------------- |
| `system_message` | string | system message for gpt-4o model. |
| `user_message`   | string | user message for gpt-4o model.   |

### Return Type