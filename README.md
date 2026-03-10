# base22bbgimport time
from web3 import Web3

RPC_URL = "https://mainnet.base.org"


def main():
    w3 = Web3(Web3.HTTPProvider(RPC_URL))

    if not w3.is_connected():
        raise RuntimeError("Cannot connect to Base RPC")

    print("Connected to Base")
    print("Streaming transactions from new blocks...\n")

    last_block = w3.eth.block_number

    while True:
        try:
            current_block = w3.eth.block_number

            if current_block > last_block:
                for block_num in range(last_block + 1, current_block + 1):

                    block = w3.eth.get_block(block_num, full_transactions=True)

                    print(f"\nBlock {block_num} | tx count: {len(block.transactions)}")

                    for tx in block.transactions[:5]:  # show first 5 tx
                        value_eth = w3.from_wei(tx.value, "ether")

                        print(
                            f"tx: {tx.hash.hex()} | "
                            f"from: {tx['from']} | "
                            f"to: {tx.to} | "
                            f"value: {value_eth} ETH"
                        )

                last_block = current_block

            time.sleep(2)

        except Exception as e:
            print("Error:", e)
            time.sleep(5)


if __name__ == "__main__":
    main()
