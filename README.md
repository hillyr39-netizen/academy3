import socket

HOST = "127.0.0.1"
PORT = 65432

server_address = (HOST, PORT)

client = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

print("Connecting...")
client.connect(server_address)
print("Connection established!")

while True:
    data = client.recv(1024)

    if not data:
        break

    message = data.decode("utf-8")
    print("Received:", message)

client.close()





import socket
import threading
from queue import Queue

HOST = "127.0.0.1"
PORT = 65432

server_address = (HOST, PORT)

messages = Queue()


def get_messages():
    while True:
        message = input("Enter a message: ")
        messages.put(message)


def send_messages(client):
    while True:
        message = messages.get()
        client.sendall(message.encode("utf-8"))


def start_server():
    server = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    server.bind(server_address)

    print("Waiting for connection...")
    server.listen(3)

    client, address = server.accept()
    print("Connected to:", address)

    send_messages(client)


def main():
    threading.Thread(target=get_messages).start()
    threading.Thread(target=start_server).start()


if __name__ == "__main__":
    main()
