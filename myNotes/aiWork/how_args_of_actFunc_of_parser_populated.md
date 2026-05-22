# How `args` is Populated When `run_trainer` is Called

## Question

How is the value of `args` populated when the function `ModelZooCLI.run_trainer` at line 233 is called in `ModelZooCLI.py`?

## Answer

The flow is straightforward:

**Line 229**: `args` is created by `parser.parse_known_args()`, which parses `sys.argv` and returns an `argparse.Namespace` object.

**Line 230**: `args.func(args)` dispatches to `run_trainer` — where `func` was registered via `set_defaults` on the subparser.

Specifically, `args` contains:

| Attribute | Source | Value |
|---|---|---|
| `args.cmd` | subparser `dest` (line 53) | `"fit"`, `"validate"`, or `"validate_all"` |
| `args.mode` | `set_defaults` (lines 77, 98, 118) | `"train_and_eval"`, `"eval"`, or `"eval_all"` |
| `args.func` | `set_defaults` (lines 76, 97, 117) | `ModelZooCLI.run_trainer` |
| `args.seen_args` | `set_defaults` (lines 78, 98, 118) | return value of `add_run_args()` |
| all CLI flags | `add_run_args(fit_parser)` (line 74) | user-provided command-line values |

So when a user runs e.g. `cszoo fit --params config.yaml`, `argparse` builds the `Namespace` from the registered arguments, injects the hardcoded defaults (`mode`, `func`, `seen_args`), and then line 230 calls `run_trainer(args)` with the fully populated namespace.

**Reference**: [`src/cerebras/modelzoo/cli/ModelZooCLI.py`](src/cerebras/modelzoo/cli/ModelZooCLI.py)
