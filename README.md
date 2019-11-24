# state-machine-std-variant-approach

```cpp
#include <iostream>
#include <variant>

class Door {
public:
	using DoorOpened = struct {};
	using DoorClosed = struct {};
	using DoorLocked = struct {};

	using DoorState = std::variant <DoorClosed, DoorOpened, DoorLocked>;

	Door(DoorOpened) { doorState = DoorOpened{}; }
	Door(DoorClosed) { doorState = DoorClosed{}; }
	Door(DoorLocked) { doorState = DoorLocked{}; }

	void open() {
		logState();
		std::clog << "Opening Door . . ." << std::endl;

		doorState = std::visit(Open{}, doorState);
		putchar('\n');
	}

	void close() {
		logState();
		std::clog << "Closing Door . . ." << std::endl;

		doorState = std::visit(Close{}, doorState);
		putchar('\n');
	}

	void lock() {
		logState();
		std::clog << "Locking Door . . ." << std::endl;

		doorState = std::visit(Lock{}, doorState);
		putchar('\n');
	}

	void unlock() {
		logState();
		std::clog << "Unlocking Door . . ." << std::endl;

		doorState = std::visit(Unlock{}, doorState);
		putchar('\n');
	}

private:
	DoorState doorState;

	void logState() {
		std::clog << "Door State : " << getState() << std::endl;
	}

	std::string getState() {
		if (std::holds_alternative <DoorOpened>(doorState)) {
			return "Open";
		}

		if (std::holds_alternative <DoorClosed>(doorState)) {
			return "Closed";
		}

		if (std::holds_alternative <DoorLocked>(doorState)) {
			return "Locked";
		}
	}

	struct Open {
		DoorState operator()(DoorOpened door) {
			std::clog << "Door is already Open" << std::endl;

			return DoorOpened{};
		}

		DoorState operator()(Door::DoorClosed door) {
			std::clog << "Door Opened" << std::endl;

			return DoorOpened{};
		}

		DoorState operator()(Door::DoorLocked door) {
			std::clog << "Cannot Open Locked Doors" << std::endl;

			return DoorLocked{};
		}
	};

	struct Close {
		DoorState operator()(Door::DoorOpened door) {
			std::clog << "Door Closed" << std::endl;

			return DoorClosed{};
		}

		DoorState operator()(Door::DoorClosed door) {
			std::clog << "Door is already Closed" << std::endl;

			return DoorClosed{};
		}

		DoorState operator()(Door::DoorLocked door) {
			std::clog << "Door is already Closed and Locked" << std::endl;

			return DoorLocked{};
		}
	};

	struct Lock {
		DoorState operator()(Door::DoorOpened door) {
			std::clog << "Cannot Lock Opened Doors" << std::endl;

			return DoorOpened{};
		}

		DoorState operator()(Door::DoorClosed door) {
			std::clog << "Door Locked" << std::endl;

			return DoorLocked{};
		}

		DoorState operator()(Door::DoorLocked door) {
			std::clog << "Door is already Locked" << std::endl;

			return DoorLocked{};
		}
	};

	struct Unlock {
		DoorState operator()(Door::DoorOpened door) {
			std::clog << "Door is already Unlocked and Opened" << std::endl;

			return DoorOpened{};
		}

		DoorState operator()(Door::DoorClosed door) {
			std::clog << "Door is already Unlocked and Closed" << std::endl;

			return DoorClosed{};
		}

		DoorState operator()(Door::DoorLocked door) {
			std::clog << "Door Unlocked" << std::endl;

			return DoorClosed{};
		}
	};

};

template <class state>
Door makeDoor() { return Door{ state{} }; }

int main() {
	Door door = makeDoor <Door::DoorClosed>();

	door.open();
	door.lock();

	door.close();
	door.lock();

	door.open();
	door.unlock();
}
```
