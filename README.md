import socket

def main():
    server_socket = socket.socket()
    server_socket.bind(("127.0.0.1", 1234))
    server_socket.listen()

    print("Waiting for connection...")
    client_socket, address = server_socket.accept()

    size_data = b""
    while len(size_data) < 4:
        part = client_socket.recv(4 - len(size_data))
        if part == b"":
            break
        size_data += part

    if len(size_data) == 4:
        file_size = int.from_bytes(size_data, byteorder="big")

        file = open("received_file.txt", "wb")

        received = 0
        while received < file_size:
            data = client_socket.recv(min(1024, file_size - received))
            if data == b"":
                break

            file.write(data)
            received += len(data)

        file.close()

    client_socket.close()
    server_socket.close()


if __name__ == "__main__":
    main()



import socket

def main():
    client_socket = socket.socket()
    client_socket.connect(("127.0.0.1", 1234))

    file = open("file.txt", "rb")
    data = file.read()
    file.close()

    file_size = len(data)

    size_data = file_size.to_bytes(4, byteorder="big")

    client_socket.sendall(size_data)
    client_socket.sendall(data)

    client_socket.close()


if __name__ == "__main__":
    main()
