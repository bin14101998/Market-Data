# Market Data
A Chainlink node serving crypto, forex, and stock market data

### How to create requests to this node
This node uses the Alpha Vantage API. Any parameters supplied will be added to a GET request to the API. For example, https://www.alphavantage.co/query?function=TIME_SERIES_INTRADAY&symbol=MSFT&interval=5min

The API returns a JSON response. Adding the desired element names to the copyPath parameter will traverse down to the corresponding JSON elements.

For more info please refer to the Alpha Vantage documentation: https://www.alphavantage.co/documentation/

### Sample node request
```
uint256 public stockResult;

event RequestStockPriceFulfilled(
    bytes32 indexed requestId,
    uint256 indexed fulfilledResult
  );

function requestStockPrice(address _oracle, string _jobId)
    public
    onlyOwner
  {
    Chainlink.Request memory req = buildChainlinkRequest(stringToBytes32(_jobId), this, this.fulfillStockPrice.selector);
    req.add("function", "GLOBAL_QUOTE");
    req.add("symbol", "AAPL");
    req.addInt("times", 100);
    string[] memory path = new string[](2);
    path[0] = "Global Quote";
    path[1] = "05. price";
    req.addStringArray("copyPath", path);
    sendChainlinkRequestTo(_oracle, req, ORACLE_PAYMENT);
  }
  
function fulfillStockPrice(bytes32 _requestId, uint256 _result)
    public
    recordChainlinkFulfillment(_requestId)
  {
    emit RequestStockPriceFulfilled(_requestId, _result);
    stockResult = _result;
  }
```
