# ByBit Data Downloader

A high-performance Python package for downloading historical data from ByBit exchange.

## Features

- ✅ Download historical trade and orderbook data
- ✅ Support for spot and contract markets  
- ✅ High-performance parallel downloads using threading
- ✅ Automatic date range splitting for large requests (7-day chunks)
- ✅ Robust error handling and retry logic with exponential backoff
- ✅ File size verification and duplicate detection
- ✅ Comprehensive logging and progress tracking
- ✅ Easy-to-use Python module with clean API

## Installation

### From Source
```bash
git clone https://github.com/AdityaLakkad/bybit_data_downloader.git
cd bybit_data_downloader
pip install -r requirements.txt
```

### Dependencies
- Python 3.8+
- httpx>=0.24.0

## Quick Start

### Basic Usage
```python
from bybit_data_downloader import ByBitHistoricalDataDownloader

# Initialize downloader with custom parallel downloads
downloader = ByBitHistoricalDataDownloader(parallel_downloads=10, timeout=30)

# Show help information
downloader.help()

# Fetch available symbols for a market
symbols = downloader.fetch_symbols('spot', 'trade')
print(f"Available symbols: {symbols[:10]}")

# Download historical data
stats = downloader.download_data(
    symbol='BTCUSDT',
    start_date='2025-07-01',
    end_date='2025-07-07',
    biz_type='spot',
    product_id='trade',
    output_dir='./bybit_data'
)

print(f"Downloaded {stats['downloaded']}/{stats['total_files']} files successfully")
```

### Import Options
```python
# Import main class directly
from bybit_data_downloader import ByBitHistoricalDataDownloader

# Import from submodule
from bybit_data_downloader.historical import ByBitHistoricalDataDownloader

# Import entire module
import bybit_data_downloader
downloader = bybit_data_downloader.ByBitHistoricalDataDownloader()
```

## API Reference

### ByBitHistoricalDataDownloader

#### Constructor Parameters
- `parallel_downloads` (int): Number of concurrent downloads (1-20 recommended, default: 5)
- `timeout` (int): Request timeout in seconds (default: 30)

#### Methods

##### `help() -> None`
Display comprehensive usage information and parameter details.

##### `fetch_symbols(biz_type: str, product_id: str) -> List[str]`
Fetch available trading symbols for specified market.

**Parameters:**
- `biz_type`: Market type ('spot' or 'contract')
- `product_id`: Data type ('trade' or 'orderbook')

**Returns:** List of available symbol strings

**Raises:** 
- `ValueError`: Invalid parameters
- `httpx.RequestError`: API request failure

##### `download_data(symbol, start_date, end_date, biz_type, product_id, output_dir) -> Dict[str, int]`
Download historical data with automatic chunking and parallel processing.

**Parameters:**
- `symbol` (str): Trading pair symbol (e.g., 'BTCUSDT', 'ETHUSDT')
- `start_date` (str): Start date in 'YYYY-MM-DD' format
- `end_date` (str): End date in 'YYYY-MM-DD' format  
- `biz_type` (str): Market type ('spot' or 'contract')
- `product_id` (str): Data type ('trade' or 'orderbook')
- `output_dir` (str): Directory to save files (default: './data')

**Returns:** Dictionary with download statistics:
```python
{
    'total_files': int,    # Total files found
    'downloaded': int,     # Successfully downloaded
    'failed': int         # Failed downloads
}
```

**Features:**
- Automatic 7-day date range splitting (API limitation)
- Parallel downloads using ThreadPoolExecutor
- File size verification and duplicate detection
- Retry logic with exponential backoff (3 attempts)
- Creates organized directory structure: `{output_dir}/{biz_type}/{product_id}/{symbol}/`

## Supported Markets

| biz_type | product_id | Description |
|----------|------------|-------------|
| spot | trade | Spot market trading data |
| spot | orderbook | Spot market orderbook data |
| contract | trade | Futures/Derivatives trading data |
| contract | orderbook | Futures/Derivatives orderbook data |

## File Organization

Downloaded files are automatically organized in this structure:
```
output_dir/
├── spot/
│   ├── trade/
│   │   └── BTCUSDT/
│   │       ├── BTCUSDT_2025-07-01_trade.csv.gz
│   │       └── BTCUSDT_2025-07-02_trade.csv.gz
│   └── orderbook/
│       └── BTCUSDT/
└── contract/
    ├── trade/
    └── orderbook/
```

## Advanced Features

### Error Handling
- Automatic retry with exponential backoff
- File size verification against API metadata
- Graceful handling of network timeouts
- Comprehensive logging for debugging

### Performance Optimization
- Parallel downloads using ThreadPoolExecutor
- Configurable concurrency levels
- Efficient memory usage with streaming downloads
- Smart duplicate file detection

### Date Range Management
- Automatic splitting of large date ranges into 7-day chunks
- Validation of date formats and logical ranges
- Efficient batch processing of multiple date ranges

## Examples

### Example 1: Basic Download
```python
from bybit_data_downloader import BybitDataDownloader

downloader = BybitDataDownloader()
stats = downloader.download_data(
    symbol='BTCUSDT',
    start_date='2025-07-01', 
    end_date='2025-07-07',
    biz_type='spot',
    product_id='trade',
    output_dir='./data'
)
```

### Example 2: High-Performance Bulk Download
```python
downloader = BybitDataDownloader(parallel_downloads=15, timeout=60)

# Download multiple months of data (automatically chunked)
stats = downloader.download_data(
    symbol='ETHUSDT',
    start_date='2025-01-01',
    end_date='2025-06-30', 
    biz_type='spot',
    product_id='trade',
    output_dir='/data/crypto'
)
```

### Example 3: Explore Available Markets
```python
downloader = BybitDataDownloader()

# Get all spot trading symbols
spot_symbols = downloader.fetch_symbols('spot', 'trade')
print(f"Spot trading symbols: {len(spot_symbols)}")

# Get contract symbols
contract_symbols = downloader.fetch_symbols('contract', 'trade')  
print(f"Contract symbols: {len(contract_symbols)}")
```

See `example_download.py` for a complete working example.

## Development

### Running Tests
```bash
python3 test_import.py
```

### Project Structure
```
bybit_data_downloader/
├── bybit_data_downloader/
│   ├── __init__.py
│   ├── historical/
│   │   ├── __init__.py
│   │   └── ByBitDataDownloader.py    # Main implementation
│   └── live/
│       └── __init__.py               # Future live data features
├── example_download.py               # Working example
├── test_import.py                    # Import verification
├── setup.py                         # Package installation
├── requirements.txt                  # Dependencies
└── README.md
```

## Technical Details

### Threading Model
- Uses `ThreadPoolExecutor` for parallel downloads
- Configurable worker thread count
- Thread-safe logging and error handling

### API Interaction
- Direct integration with ByBit's historical data API
- Proper HTTP headers mimicking browser requests
- Automatic rate limiting through thread pool size

### Data Integrity
- SHA/size verification for downloaded files
- Automatic cleanup of corrupted downloads
- Resume capability for interrupted downloads

## Troubleshooting

### Common Issues
1. **Network Timeouts**: Increase `timeout` parameter
2. **Too Many Failures**: Reduce `parallel_downloads` count
3. **Disk Space**: Monitor free space for large date ranges
4. **API Rate Limits**: Use default `parallel_downloads=5` or lower

### Logging
Enable detailed logging to diagnose issues:
```python
import logging
logging.basicConfig(level=logging.DEBUG)
```

## License

MIT License

## Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/new-feature`)
3. Commit changes (`git commit -am 'Add new feature'`)
4. Push to branch (`git push origin feature/new-feature`)
5. Create Pull Request

## Changelog

### Version 1.0.0
- Initial release with historical data download
- Synchronous implementation with threading
- Automatic date range chunking
- Robust error handling and retry logic
- File organization and integrity verification
