From [Qt Documentation](http://doc.qt.io/archives/qt-4.8/signalsandslots.html):

> Since slots are normal member functions, they follow the normal C++ rules when called directly. However, as slots, they can be invoked by any component, regardless of its access level, via a signal-slot connection. This means that a signal emitted from an instance of an arbitrary class can cause a private slot to be invoked in an instance of an unrelated class.

***What this means:*** From another class, you can't call a private slot as a function, but if you emit a signal connected to that private slot, you can invoke it.
