---
path: /blog/GOOS
date: 2020-04-16T18:03:51.309Z
title: Thorough unit testing improves code quality
description: A blog about unit testing using mock objects
---
I use mock objects to eliminate dependencies when unit testing. I isolate the class being tested, and if I can mock dependencies easily, I feel confident I designed the application well. This approach, described in “Growing Object-Oriented Software, Guided by Tests” (GOOS), combines Test-Driven Development with mock objects. GOOS promotes TDD as a way of writing better designed, cleaner, and more modular code. See this [blog](https://friendly-archimedes-6b3170.netlify.com/blog/tdd/) for more details about TDD.

GOOS describes unit testing as a gauge of a system’s internal quality. Internal quality refers to the class’ design, and whether the class behaves in the way intended. Thorough unit tests signal high internal quality, and a good design, where classes are loosely coupled and change happens easily.  To unit test a class, the class must have easily substituted dependencies, and clear responsibilities. Unit tests do not provide information about whether a system works as a whole. Instead, use acceptance or end to end tests for this measure.

In a unit test, we create the object, provide its dependencies in the form of mock objects, interact with it, and check it behaved as expected. Dependencies include objects a class communicates with directly. In Java, interfaces define roles, the communication between objects, and the services objects require. Interfaces allow substitution of dependencies with mock objects.  For example, see below an Echoer class.

```java
public class Echoer {
   private String message;

   public Echoer(String message) {
       this.message = message;
   }

   public void echo(Connection connection) throws IOException {
       String input;
       connection.write(this.message);
       while ((input = connection.read()) != null) {
           connection.write(input);
       }
   }
}

```

The Echoer class depends on a String message and a Connection connection, which represents an interface. See below the Connection interface, and the class SocketConnection, implementing this interface. This application passes an instance of the SocketConnection class into the echo method.

```java
public interface Connection {
   String read() throws IOException;

   void write(String output) throws IOException;

   public void close() throws IOException;

   public boolean isClosed();
}

public class SocketConnection implements Connection{
   private final Socket socket;

   public SocketConnection(Socket socket) {
       this.socket = socket;
   }

   public String read() throws IOException {
       return new BufferedReader(new InputStreamReader(socket.getInputStream())).readLine();
   }

   public void write(String output) throws IOException {
       String formattedOutput = output + "\n";
       socket.getOutputStream().write(formattedOutput.getBytes());
   }

   public void close() throws IOException {
       this.socket.close();
   }

   public boolean isClosed() {
       return this.socket.isClosed();
   }
}
```

To isolate the Echoer class, we substitute the String message, representing a welcome message, for any other welcome message, and the SocketConnection class for a mock object implementing the Connection interface. We test the Echoer’s behavior of welcoming the user by calling the write method of the Connection interface, of reading and writing the user’s input by calling the write and read methods of the Connection interface, and of ending the echoing service once receiving null input from the user. We structure the mock object, TestConnection, to enable this testing by initializing it with a set of inputs, and documenting the set of outputs. See the Echoer unit test code below.

```java
class EchoerTest {
   @Test
   void itEchosTextAfterWelcomingTheUser() throws IOException {
       Echoer echoer = new Echoer("Welcome");
       ArrayList<String> input = new ArrayList<String>(Arrays.asList("This", "Should", "be", "echoed"));
       TestConnection connection = new TestConnection(input);
       echoer.echo(connection);

       assertEquals(Arrays.asList("Welcome", "This", "Should", "be", "echoed"), connection.written);
   }

   private class TestConnection implements Connection {

       public ArrayList<String> written;
       private ArrayList<String> input;

       public TestConnection(ArrayList<String> input) {
           this.input = input;
           this.written = new ArrayList<String>();
       }

       public String read() {
           if(this.input.size() > 0) {
               return this.input.remove(0);
           } else {
               return null;
           }
       }

       public void write(String output) {
           this.written.add(output);
       }

       public void close() {
       }

       public boolean isClosed() {
           return false;
       }
   }
}
```

GOOS recommends only mock types you own. For example, when working with third party libraries, we don’t have a deep understanding or control of the code, and therefore mocks introduce complexity and risk. Instead, wrap these third party libraries in an adapter object, which implements an interface representing the services objects need. In the example above, the SocketConnection class represents an adapter object, wrapping the Java Socket Class. We mock the SocketConnection class via the Connection interface when testing objects depending on this class. To unit test the wrapper, use the real library or mock the library if you can’t trigger a behavior with the real library. These types of mocks are few. See below the SocketConnection unit test using a mock of the Java Socket Class.

```java
class SocketConnectionTest {
   @Test
   void itReadsFromInputStream() throws IOException {
       String input = "Connected";
       Socket socket = new FakeSocket(input);
       SocketConnection socketConnection = new SocketConnection(socket);

       assertEquals(input, socketConnection.read());
   }

   @Test
   void itWritesToOutputStream() throws IOException {
       String input = "Connected";
       Socket socket = new FakeSocket(input);
       SocketConnection socketConnection = new SocketConnection(socket);

       socketConnection.write(input);

       assertEquals(input + "\n", socket.getOutputStream().toString());
   }

   @Test
   void itClosesConnection() throws IOException {
       Socket socket = new FakeSocket("Connected");
       SocketConnection socketConnection = new SocketConnection(socket);

       socketConnection.close();

       assertTrue(socketConnection.isClosed());
   }

   @Test
   void itReturnsFalseWhenConnectionIsOpen() {
       Socket socket = new FakeSocket("Connected");
       SocketConnection socketConnection = new SocketConnection(socket);

       assertFalse(socketConnection.isClosed());
   }

   private class FakeSocket extends Socket {

       private final ByteArrayOutputStream output;
       private final ByteArrayInputStream input;

       public FakeSocket(String input) {
           this.output = new ByteArrayOutputStream();
           this.input = new ByteArrayInputStream((input.getBytes()));
       }

       public OutputStream getOutputStream() {
           return this.output;
       }

       public InputStream getInputStream() {
           return this.input;
       }
   }
}
```

Thorough unit testing improves system internal quality, and using mock objects allows for effective unit testing.
