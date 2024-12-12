# WebSocket Listener for BTC/USDT Prices (with Aggregation)

This Rust project connects to the Binance WebSocket to listen for real-time BTC/USDT trade prices. It supports two modes: **cache mode** and **read mode**. In **cache mode**, multiple clients fetch real-time prices, calculate average prices individually, and send the averages to an aggregator process that computes the global average. In **read mode**, previously saved data (from **cache mode**) is read and displayed.

---

## Features

- **Cache Mode**: 
  - Multiple clients listen to the WebSocket for a set duration.
  - Each client computes the average BTC price from the data it receives.
  - Clients send their average prices to an aggregator, which computes the global average.
  - All data (individual client averages and global average) is saved to files.
  
- **Read Mode**:
  - Reads and displays saved price data (individual client data and the global average) from text files.
  
- **WebSocket Streaming**:
  - Connects to the Binance WebSocket to receive real-time BTC/USDT prices.
  
---

## Requirements

1. **Rust**: Make sure you have Rust installed. If not, you can install it from [here](https://www.rust-lang.org/tools/install).
2. **Dependencies**:
   - `futures`
   - `tokio`
   - `tokio-tungstenite`
   - `serde`
   - `serde_json`
   - `clap`

These dependencies are specified in the `Cargo.toml` file.

---

## Installation

1. Clone the repository:

   ```bash
   git clone https://github.com/wdcs-pruthvithakor/rust-multi-client.git
   ```

2. Navigate into the project directory:

   ```bash
   cd rust-multi-client
   ```

3. Build the project:

   ```bash
   cargo build --release
   ```

---

## Usage

### 1. Running in **cache mode**

In **cache mode**, the program launches multiple clients, each connecting to the WebSocket and listening for BTC/USDT price updates for a specified number of seconds. The clients compute an average price of the received data, and then the aggregator computes the global average of all client averages. All data is saved to files.

To run the program in **cache mode**:

```bash
cargo run -- --mode cache --times <seconds>
```

- `--mode cache`: Specifies that the program should run in cache mode.
- `--times <seconds>`: Specifies the number of seconds each client should listen for WebSocket messages (default is `1` second).

Example:

```bash
cargo run -- --mode cache --times 10
```

This will start 5 clients, each listening to the WebSocket for 10 seconds, and then compute and save the data.

---

### 2. Running in **read mode**

In **read mode**, the program reads and displays the previously saved data (price points and averages) from text files.

To run the program in **read mode**:

```bash
cargo run -- --mode read
```

This will read and display the saved price data from the following files:
- `client_1_data.txt`, `client_2_data.txt`, ..., `client_5_data.txt` (or however many clients you run)
- `global_data.txt`

---

## File Outputs

- **client_{id}_data.txt**: Contains the price data points and calculated average for each client.
  
  Example content:
  ```
  Prices: [34912.45, 34914.32, 34910.12]
  Average: 34912.30
  ```

- **global_data.txt**: Contains the individual client averages and the global average price.
  
  Example content:
  ```txt
  Client Averages: [34912.30, 34915.12, 34911.45]
  Global Average: 34912.29
  ```

---

## Code Overview

### **Functions**:

- **`client_process`**: A function representing the logic for each client. It connects to the WebSocket, collects BTC prices for a given duration, computes the average price, and sends it to the aggregator.
  
- **`aggregator_process`**: Aggregates the average BTC prices from all clients and computes a global average. It saves both the client averages and the global average to files.

- **`connect_to_websocket`**: Establishes a connection to the Binance WebSocket server to receive real-time BTC/USDT prices.

- **`process_message`**: Processes the WebSocket messages and extracts the BTC price from the message.

- **`calculate_average`**: Calculates the average price from a vector of prices.

- **`save_client_data`**: Saves each client's data (price points and average) to a text file.

- **`save_global_data`**: Saves the global data (individual client averages and global average) to a text file.

- **`parse_arguments`**: Parses command-line arguments using `clap`.

- **`read_mode`**: Reads and prints the saved data from the text files.

---

## Error Handling

The program handles various types of errors:
- **WebSocket connection errors**: If a client fails to connect to the WebSocket, it prints an error message.
- **Message processing errors**: If a message does not contain a valid BTC price, it reports an error.
- **File handling errors**: If reading or writing files fails, appropriate error messages are displayed.

---

## Contribution

Feel free to fork the repository, submit issues, or create pull requests to contribute to the project!

---

## License

This project is open-source and available under the [MIT License](LICENSE).

---

## Example

Run the program in **cache mode** for 10 seconds and save the results:

```bash
cargo run -- --mode cache --times 10
```

After completion, you can read the saved price data and averages by running:

```bash
cargo run -- --mode read
```
