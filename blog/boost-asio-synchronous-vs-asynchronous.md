***References:*** https://www.boost.org/doc/libs/1_58_0/doc/html/boost_asio/overview/core/basics.html

Boost.Asio may be used to perform both synchronous and asynchronous operations on I/O objects such as sockets. Before using Boost.Asio it may be useful to get a conceptual picture of the various parts of Boost.Asio, your program, and how they work together.

As an introductory example, let's consider what happens when you perform a connect operation on a socket. We shall start by examining synchronous operations.

***Your program*** will have at least one ***io_service*** object. The ***io_service*** represents ***your program***'s link to the ***operating system***'s I/O services.

```c++
boost::asio::io_service io_service;
```

To perform I/O operations ***your program*** will need an ***I/O object*** such as a TCP socket:

```c++
boost::asio::ip::tcp::socket socket(io_service);
```

## Synchronous

![](../img/boost-asio-synchronous-vs-asynchronous/fig1.png?raw=true)

When a synchronous connect operation is performed, the following sequence of events occurs:

1. ***Your program*** initiates the connect operation by calling the ***I/O object***:
    ```c++
    socket.connect(server_endpoint);
    ```

2. The ***I/O object*** forwards the request to the ***io_service***.

3. The ***io_service*** calls on the ***operating system*** to perform the connect operation.

4. The ***operating system*** returns the result of the operation to the ***io_service***.

5. The ***io_service*** translates any error resulting from the operation into an object of type `boost::system::error_code`. An *error_code* may be compared with specific values, or tested as a boolean (where a false result means that no error occurred). The result is then forwarded back up to the ***I/O object***.

6. The ***I/O object*** throws an exception of type `boost::system::system_error` if the operation failed. If the code to initiate the operation had instead been written as:
    ```c++
    boost::system::error_code ec;
    socket.connect(server_endpoint, ec);
    ```
    then the *error_code* variable *ec* would be set to the result of the operation, and no exception would be thrown.

## Asynchronous

![](../img/boost-asio-synchronous-vs-asynchronous/fig2.png?raw=true)

When an asynchronous operation is used, a different sequence of events occurs.

1. ***Your program*** initiates the connect operation by calling the ***I/O object***:
    ```c++
    socket.async_connect(server_endpoint, your_completion_handler);
    ```
    where *your_completion_handler* is a function or function object with the signature:
    ```c++
    void your_completion_handler(const boost::system::error_code& ec);
    ```
    The exact signature required depends on the asynchronous operation being performed. The reference documentation indicates the appropriate form for each operation.

2. The ***I/O object*** forwards the request to the ***io_service***.

3. The ***io_service*** signals to the ***operating system*** that it should start an asynchronous connect.

Time passes. (In the synchronous case this wait would have been contained entirely within the duration of the connect operation.)

![](../img/boost-asio-synchronous-vs-asynchronous/fig3.png?raw=true)

4. The ***operating system*** indicates that the connect operation has completed by placing the result on a queue, ready to be picked up by the ***io_service***.

5. ***Your program*** must make a call to `io_service::run()` (or to one of the similar ***io_service*** member functions) in order for the result to be retrieved. A call to `io_service::run()` blocks while there are unfinished asynchronous operations, so you would typically call it as soon as you have started your first asynchronous operation.

6. While inside the call to `io_service::run()`, the ***io_service*** dequeues the result of the operation, translates it into an error_code, and then passes it to ***your completion handler***.

## Example 1: A synchronous TCP daytime client

```c++
//
// client.cpp
// ~~~~~~~~~~
//
// Copyright (c) 2003-2015 Christopher M. Kohlhoff (chris at kohlhoff dot com)
//
// Distributed under the Boost Software License, Version 1.0. (See accompanying
// file LICENSE_1_0.txt or copy at http://www.boost.org/LICENSE_1_0.txt)
//

#include <iostream>
#include <boost/array.hpp>
#include <boost/asio.hpp>

using boost::asio::ip::tcp;

int main(int argc, char* argv[])
{
  try
  {
    if (argc != 2)
    {
      std::cerr << "Usage: client <host>" << std::endl;
      return 1;
    }

    boost::asio::io_service io_service;

    tcp::resolver resolver(io_service);
    tcp::resolver::query query(argv[1], "daytime");
    tcp::resolver::iterator endpoint_iterator = resolver.resolve(query);

    tcp::socket socket(io_service);
    boost::asio::connect(socket, endpoint_iterator);

    for (;;)
    {
      boost::array<char, 128> buf;
      boost::system::error_code error;

      size_t len = socket.read_some(boost::asio::buffer(buf), error);

      if (error == boost::asio::error::eof)
        break; // Connection closed cleanly by peer.
      else if (error)
        throw boost::system::system_error(error); // Some other error.

      std::cout.write(buf.data(), len);
    }
  }
  catch (std::exception& e)
  {
    std::cerr << e.what() << std::endl;
  }

  return 0;
}
```

## Example 2: A synchronous TCP daytime server

```c++
//
// server.cpp
// ~~~~~~~~~~
//
// Copyright (c) 2003-2015 Christopher M. Kohlhoff (chris at kohlhoff dot com)
//
// Distributed under the Boost Software License, Version 1.0. (See accompanying
// file LICENSE_1_0.txt or copy at http://www.boost.org/LICENSE_1_0.txt)
//

#include <ctime>
#include <iostream>
#include <string>
#include <boost/asio.hpp>

using boost::asio::ip::tcp;

std::string make_daytime_string()
{
  using namespace std; // For time_t, time and ctime;
  time_t now = time(0);
  return ctime(&now);
}

int main()
{
  try
  {
    boost::asio::io_service io_service;

    tcp::acceptor acceptor(io_service, tcp::endpoint(tcp::v4(), 13));

    for (;;)
    {
      tcp::socket socket(io_service);
      acceptor.accept(socket);

      std::string message = make_daytime_string();

      boost::system::error_code ignored_error;
      boost::asio::write(socket, boost::asio::buffer(message), ignored_error);
    }
  }
  catch (std::exception& e)
  {
    std::cerr << e.what() << std::endl;
  }

  return 0;
}
```

## Example 3: An asynchronous TCP daytime server

```c++
//
// server.cpp
// ~~~~~~~~~~
//
// Copyright (c) 2003-2015 Christopher M. Kohlhoff (chris at kohlhoff dot com)
//
// Distributed under the Boost Software License, Version 1.0. (See accompanying
// file LICENSE_1_0.txt or copy at http://www.boost.org/LICENSE_1_0.txt)
//

#include <ctime>
#include <iostream>
#include <string>
#include <boost/bind.hpp>
#include <boost/shared_ptr.hpp>
#include <boost/enable_shared_from_this.hpp>
#include <boost/asio.hpp>

using boost::asio::ip::tcp;

std::string make_daytime_string()
{
  using namespace std; // For time_t, time and ctime;
  time_t now = time(0);
  return ctime(&now);
}

class tcp_connection
  : public boost::enable_shared_from_this<tcp_connection>
{
public:
  typedef boost::shared_ptr<tcp_connection> pointer;

  static pointer create(boost::asio::io_service& io_service)
  {
    return pointer(new tcp_connection(io_service));
  }

  tcp::socket& socket()
  {
    return socket_;
  }

  void start()
  {
    message_ = make_daytime_string();

    boost::asio::async_write(socket_, boost::asio::buffer(message_),
        boost::bind(&tcp_connection::handle_write, shared_from_this(),
          boost::asio::placeholders::error,
          boost::asio::placeholders::bytes_transferred));
  }

private:
  tcp_connection(boost::asio::io_service& io_service)
    : socket_(io_service)
  {
  }

  void handle_write(const boost::system::error_code& /*error*/,
      size_t /*bytes_transferred*/)
  {
  }

  tcp::socket socket_;
  std::string message_;
};

class tcp_server
{
public:
  tcp_server(boost::asio::io_service& io_service)
    : acceptor_(io_service, tcp::endpoint(tcp::v4(), 13))
  {
    start_accept();
  }

private:
  void start_accept()
  {
    tcp_connection::pointer new_connection =
      tcp_connection::create(acceptor_.get_io_service());

    acceptor_.async_accept(new_connection->socket(),
        boost::bind(&tcp_server::handle_accept, this, new_connection,
          boost::asio::placeholders::error));
  }

  void handle_accept(tcp_connection::pointer new_connection,
      const boost::system::error_code& error)
  {
    if (!error)
    {
      new_connection->start();
    }

    start_accept();
  }

  tcp::acceptor acceptor_;
};

int main()
{
  try
  {
    boost::asio::io_service io_service;
    tcp_server server(io_service);
    io_service.run();
  }
  catch (std::exception& e)
  {
    std::cerr << e.what() << std::endl;
  }

  return 0;
}
```

