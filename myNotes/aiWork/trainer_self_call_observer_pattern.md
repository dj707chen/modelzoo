# Trainer `self.call` — Observer Pattern

**Q: So `self.call` just sends events to the receivers in `self.callbacks`?**

Yes, exactly. `self.call` is just a simple event broadcaster — it loops over every callback in `self.callbacks` and calls the named method on each one that has it. It's essentially a manual observer pattern with no registration of hook names, just duck typing on method names.

**Q: So `self.call` is just a user-defined function, not a Python builtin facility?**

Correct. `self.call` is just a regular method defined on the `Trainer` class at `trainer.py:384`. The Cerebras team wrote it themselves to implement the observer/event pattern. Python has no built-in event/callback dispatching — this is all custom code.
