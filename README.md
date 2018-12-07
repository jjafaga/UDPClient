# UDPClient
# UDP client socket code used to communicate with a UDP server code
#include <iostream>
#include <string>
#include <ws2tcpip.h>

#pragma comment (lib, "ws2_32.lib")
using namespace std;

void main(int argc, char* argv[])	// Needed to pass in command line option
{
	const int twn = 1024, twe = 256;

	// Winsock Startup
	WSADATA data;
	WORD version = MAKEWORD(2, 2);
	int trigger = WSAStartup(version, &data);

	if (trigger != 0)
	{
		cout << "Can't start Winsock!" << trigger;
		return;
	}

	// Create hint structure for the server 
	sockaddr_in server;
	server.sin_family = AF_INET;
	server.sin_port = htons(54000);		// Conversion from little to big endian
	inet_pton(AF_INET, "192.168.1.100", &server.sin_addr);		// String to network

	// Socket Creation
	SOCKET out = socket(AF_INET, SOCK_DGRAM, 0);

	// Write out to that socket
	string s(argv[1]);
	int sendOk = sendto(out, s.c_str(), s.size() + 1, 0, (sockaddr*)&server, sizeof(server));

	if (sendOk == SOCKET_ERROR)
		cout << "That didn't work!" << WSAGetLastError() << endl;
	
	// Close the socket
	closesocket(out); 

	// Close down Winsock
	WSACleanup();
}
