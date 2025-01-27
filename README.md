# SwiftyModbus

This is a Swift wrapper class for the [*libmodbus library*](http://libmodbus.org) with support for TCP and RTU connections.
Supports both macOS and Linux.

## How To Get Started

- Install libmodbus library
```bash
brew install libmodbus
```
or
```bash
apt install libmodbus-dev
```

- Add   
`.package(url: "https://github.com/DimaRU/SwiftyModbus.git", .upToNextMajor(from: "2.1.0"))` 
 to your dependencies.
 
#### SwiftyModbus now have three encapsulations
- PromiseKit: `dependencies: [.product(name: "SwiftyModbusPromise", package: "SwiftyModbus")]`
- Result: `dependencies: [.product(name: "SwiftyModbus", package: "SwiftyModbus")]`
- Throwable: `dependencies: [.product(name: "SwiftyModbus", package: "SwiftyModbus")]`

## Documentation

Autogenerated documentation [https://dimaru.github.io/SwiftyModbus/](https://dimaru.github.io/SwiftyModbus)


## Examples

### With PromiseKit:

``` swift
import Foundation
import SwiftyModbusPromise
import PromiseKit

let modbus = SwiftyModbusPromise(address: "127.0.0.1", port: 503)
modbus.responseTimeout = 1.5      // Set timeout for 1.5 sec
print(modbus.responseTimeout)

firstly {
    modbus.connect()
}.then {
    modbus.readRegisters(addr: 102, count: 10)
}.done { registers in
    print(registers)
}.then {
    modbus.writeRegisters(addr: 102, data: [1,2,3,4,5,6,7,8,9,10])
}.then {
    modbus.readInputRegisters(addr: 102, count: 10)
}.done { registers in
    print(registers)
}.then {
    modbus.writeBits(addr: 104, status: [0,1,0,1])
}.then {
    modbus.readBits(addr: 104, count: 4)
}.done { bits in
    print(bits)
}.then {
    modbus.disconnect()
}.catch { error in
    print(error)
}.finally {
    exit(0)
}

RunLoop.main.run()
```

### Throwable:

``` swift
import Foundation
import SwiftyModbus

let modbusQueue = DispatchQueue(label: "queue.modbus")

modbusQueue.async {
    modbusIO()
}
RunLoop.main.run()

func modbusIO() {
    let modbus = SwiftyModbus(address: "127.0.0.1", port: 503)
    modbus.responseTimeout = 1.5      // Set response timeout for 1.5 sec
    print("Modbus responce timeout:", modbus.responseTimeout)

    do {
        try modbus.connect()
        let registers = try modbus.readRegisters(addr: 102, count: 10)
        print(registers)
        try modbus.writeRegisters(addr: 102, data: [1,2,3,4,5,6,7,8,9,10])
        let inputRegisters = try modbus.readInputRegisters(addr: 102, count: 10)
        print(inputRegisters)
        try modbus.writeBits(addr: 104, status: [0,1,0,1])
        let bits = try modbus.readBits(addr: 104, count: 4)
        print(bits)
        modbus.disconnect()
        exit(0)
    } catch {
        print(error)
        exit(1)
    }
}
```

### RTU
``` swift
import Foundation
import SwiftyModbus

let modbusQueue = DispatchQueue(label: "queue.modbus")

modbusQueue.async {
    modbusIO()
}
RunLoop.main.run()

func modbusIO() {
    let modbus = SwiftyModbus(device: "/dev/ttyUSB0", baudRate: 9600, parity: .even, byteSize: .eight, stopBits: .one)
    modbus.responseTimeout = 1.5      // Set response timeout for 1.5 sec
    print("Modbus responce timeout:", modbus.responseTimeout)

    do {
        try modbus.connect()
        let registers = try modbus.readRegisters(addr: 102, count: 10)
        print(registers)
        try modbus.writeRegisters(addr: 102, data: [1,2,3,4,5,6,7,8,9,10])
        let inputRegisters = try modbus.readInputRegisters(addr: 102, count: 10)
        print(inputRegisters)
        try modbus.writeBits(addr: 104, status: [0,1,0,1])
        let bits = try modbus.readBits(addr: 104, count: 4)
        print(bits)
        modbus.disconnect()
        exit(0)
    } catch {
        print(error)
        exit(1)
    }
}
```



You may test this example with [diagslave](https://www.modbusdriver.com/diagslave.html)

```
sudo ./diagslave -m tcp -p 503
```

## Sample modbus project

[https://github.com/DimaRU/SampleSwiftyModbus.git](https://github.com/DimaRU/SampleSwiftyModbus.git)
