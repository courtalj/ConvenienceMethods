# ConvenienceMethods
C++ Core Language Proposal for "Convenience Methods"

Convenience Method Declaration

In C++ only the methods defined in the class declaration are available to be used. This means that any functionality beyond that must be declared as a separate function that takes an instance of a class as well as any extra parameters. This proposal is for core language syntax that allows the addition of special 'method' functions for classes outside of the class declaration itself. These functions act like regular methods, but since they are not declared in the actual class they only have access to the public members of the class they operate on. In addition to regular methods, operators would be supported as well. Since these functions would not have access to private members and can easily be accomplished through other means, they are essentially for 'convenience'.

Scenario: pretend that we are dealing with a project using a library for handling geographic information, and a library for doing 3d rendering. Two separate libraries with their own namespaces. They have these two analogous structures:

namespace SomeGeoLibrary {
    struct LLA
    {
        int lat;
        int lon;
        int alt;
    };
}

namespace Some3dLibrary {
    struct vec3
    {
        int x;
        int y;
        int z;
        
        int usefulMethod() {
            return x+y+z;
        }
    };
}

As you can see, usefulMethod() exists for one and not the other. There is also no good way to convert between the two when passing them to functions. I am proposing syntax be added to the core C++ language as described below:

SomeGeoLibrary::LLA myLLA;
//myLLA.usefulMethod(); // compiler error - options are to do it manually, or convert to a vec3 first.

//Declare the usefulMethod() convenience method for the LLA class:
int usefulMethod [SomeGeoLibrary::LLA] () {
    return lat+lon+alt;
}

myLLA.usefulMethod(); // This is now legal.

The syntax starts out as a normal function, but after the function name there is an extra pair of square brackets. Between these brackets is the class for which this convenience method is to be made available. If that class were templated, the declaration would expand accordingly:

template<class T> T usefulMethod [SomeNamespace::SomeClass<T>] () {...};
template<> int usefulMethod [SomeNamespace::SomeClass<int>] () {...};

In addition to normal convenience methods, overloaded operators would be supported. The typecast operator is particularly useful.

operator Some3dLibrary::vec3 [SomeGeoLibrary::LLA] {
    vec3 ret;
    ret.x = lat;
    ret.y = lon;
    ret.z = alt;
    return ret;
}

// You could now pass a LLA object to a function expecting a vec3.

Any convenience method that conflicts with a function already declared in a class definition would be a compile error.

