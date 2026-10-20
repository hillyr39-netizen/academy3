import os
import requests
import time

virus_total_api_scan_file_url = "https://www.virustotal.com/api/v3/files"
virus_total_api_key = "3ce3ae1e6c103b247401adfe548cb76317796d2ed462b8803414abe8405bdce1"


def scan_file(file_path):
    print(f"Scanning file: {file_path}")

    analysis_id = upload_file_for_scan(file_path)
    print(f"File uploaded for scanning. Analysis ID: {analysis_id}")

    get_scan_analysis(analysis_id)


def upload_file_for_scan(file_path):
    response = requests.post(
        virus_total_api_scan_file_url,
        headers={"x-apikey": virus_total_api_key},
        files={"file": open(file_path, "rb")}
    )

    return response.json()["data"]["id"]


def get_scan_analysis(analysis_id):
    url = "https://www.virustotal.com/api/v3/analyses/" + analysis_id

    while True:
        response = requests.get(
            url,
            headers={"x-apikey": virus_total_api_key}
        )

        result = response.json()
        status = result["data"]["attributes"]["status"]

        if status == "completed":
            stats = result["data"]["attributes"]["stats"]

            if stats["malicious"] > 0:
                print("Virus detected!")
            else:
                print("No malicious detections found.")

            break

        time.sleep(10)


def iterate_directory_contents(directory_path):
    for item in os.listdir(directory_path):
        new_path = os.path.join(directory_path, item)

        if os.path.isfile(new_path):
            scan_file(new_path)
        else:
            iterate_directory_contents(new_path)


def main():
    path = r"C:\Users\admin\Downloads"
    iterate_directory_contents(path)


if __name__ == "__main__":
    main()
