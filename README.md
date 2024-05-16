# Crypto Toolbox

Useful services for cryptocurrency quant trading.

Currently, the services included are:
- [BHDS](#bhds): Binance Historical Data Service. BHDS downloads historical market data from Binance AWS data center and market data api, and converts raw data into Pandas Parquet format.

## Dependencies

Please refer to the `environment.yml` file for the setup of the conda environment.

By default, the BHDS service uses `$HOME/crypto_data` as the base directory. All data will be downloaded into this base directory. The base directory can be changed by setting the environment variable `CRYPTO_BASE_DIR`.

The BHDS service requires `aria2`, a lightweight cross-platform download utility. 
Linux/MacOS x86_64 users may install it with `conda` or through system package managers:

``` bash
conda install -c conda-forge aria2
```

For Arm64 MacOS users, installation can be done with:

``` bash
brew install aria2
```

## BHDS

Please refer to `binance_data.sh` for examples of using BHDS

### Download candlestick data and verify checksum

```bash
# Download 1H candlestick data for all usdt perpetual symbols
python cli.py bhds get_aws_all_usdt_perpetual 1h
# Verify 1H usdt perpetual candlestick data and delete corrupted
python cli.py bhds verify_aws_candle usdt_futures 1h

# Download 1H candlestick data for all coin perpetual symbols
python cli.py bhds get_aws_all_coin_perpetual 1h
# Verify 1H coin perpetual candlestick data and delete corrupted
python cli.py bhds verify_aws_candle coin_futures 1h

# Download 1H candlestick data for all usdt spot symbols
python cli.py bhds get_aws_all_usdt_spot 1h
# Verify 1H spot candle data and delete corrupted
python cli.py bhds verify_aws_candle spot 1h
```

### Download missing candlestick data from market data api

```bash
# Download usdt perpetual missing 1H candlestick data from market data api 
python cli.py bhds download_aws_missing_candle usdt_futures 1h

# Download spot missing 1H candlestick data from market data api
python cli.py bhds download_aws_missing_candle spot 1h

# Download coin perpetual missing 1H candlestick data from market data api
python cli.py bhds download_aws_missing_candle coin_futures 1h
```

### Merge downloaded candlestick csv files and convert to Pandas Parquet format

```bash
# Convert 1H usdt perpetual candlestick data to Pandas Parquet
python cli.py bhds convert_aws_candle_csv usdt_futures 1h

# Convert 1H coin perpetual candlestick data to Pandas Parquet
python cli.py bhds convert_aws_candle_csv coin_futures 1h

# Convert 1H spot candlestick data to Pandas Parquet
python cli.py bhds convert_aws_candle_csv spot 1h
```

### Split candlestick and fill gaps

```bash
# Split 1H usdt perpetual candlestick and fill gaps
python cli.py bhds fix_candle aws usdt_futures 1h

# Split 1H spot candlestick and fill gaps
python cli.py bhds fix_candle aws spot 1h

# Split 1H coin perpetual candlestick and fill gaps
python cli.py bhds fix_candle aws coin_futures 1h
```

### Download aggtrades for recent days and verify checksum

```bash
# Download recent 30 days aggtrades data for given symbol
python cli.py bhds get_aws_aggtrades usdt_futures --recent=30 BTCUSDT ETHUSDT
# Verify aggtrades data and delete corrupted
python cli.py bhds verify_aws_aggtrades usdt_futures
```

After the download procedure has successfully finished, the structure under `$CRYPTO_BASE_DIR` should look like:

```
CRYPTO_BASE_DIR
└── ./binance_data
    ├── api_data                Downloaded from market api
    │   ├── coin_futures
    │   │   └── 1h
    │   ├── spot
    │   │   └── 1h
    │   └── usdt_futures
    │       └── 1h
    ├── aws_data                Downloaded from aws data center
    │   └── data
    │       ├── futures
    │       └── spot
    ├── candle_parquet          Merged parquet data
    │   ├── coin_futures
    │   │   └── 1h
    │   ├── spot
    │   │   └── 1h
    │   └── usdt_futures
    │       └── 1h
    └── candle_parquet_fixed    Splited parquet data with gaps filled
        ├── coin_futures
        │   └── 1h
        ├── spot
        │   └── 1h
        └── usdt_futures
            └── 1h
```
