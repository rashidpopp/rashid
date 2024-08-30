import socket
import threading

# إعدادات الخادم
server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
server_socket.bind(('0.0.0.0', 12345))
server_socket.listen(5)

clients = []

# بث الرسائل لجميع العملاء
def broadcast(message, client_socket):
    for client in clients:
        if client != client_socket:
            try:
                client.send(message)
            except:
                clients.remove(client)

# التعامل مع عميل جديد
def handle_client(client_socket):
    while True:
        try:
            message = client_socket.recv(1024)
            broadcast(message, client_socket)
        except:
            clients.remove(client_socket)
            client_socket.close()
            break

# قبول الاتصالات الجديدة
def accept_connections():
    while True:
        client_socket, client_address = server_socket.accept()
        print(f"New connection: {client_address}")
        clients.append(client_socket)
        thread = threading.Thread(target=handle_client, args=(client_socket,))
        thread.start()

accept_connections()
