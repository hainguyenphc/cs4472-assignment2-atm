# CS 4472 Assignment 2: White-Box Testing

## Transfer fee (`FeesCalculator.calculateTransferFee`): basis path testing

### Process

1. **Build the control-flow graph** from the code as it is, one node per executable statement,
   named by its source line number.
2. **Compute the cyclomatic complexity** V(G) in three independent ways to fix the number of basis
   paths we need (16).
3. **Derive a basis set** of 16 linearly independent paths with McCabe's baseline method, and check
   that each path adds a new edge and that the set covers every edge.
4. **Write down the input conditions** that force execution down each path.
5. **Map each path to the specification rule** whose conditions it satisfies. That rule, not the
   code, gives the expected fee.
6. **Pick one test case per path** from its conditions, compute the expected fee from the
   specification, and run it as a JUnit test against `calculateTransferFee`.

### Assumptions

- **TA1 - Code under test.** The CFG is drawn from `src/main/java/bank/utils/FeesCalculator.java`
  as provided in the course repository (commit `a617ec3`); all line numbers refer to that file.
- **TA2 - Node granularity.** One node per executable statement. Line 77 (`double feePercentage;`)
  declares but does not assign, so it is merged into the entry node 75. Comments, blank lines and
  braces are not nodes. Lines 142 and 144 are kept as separate nodes; merging them would remove one
  node and one edge and leave V(G) unchanged.
- **TA3 - Simple predicates.** Every decision tests a single condition (no `&&` or `||`), so no
  decision has to be split into several nodes before counting.
- **TA4 - Feasibility.** The four parameters are independent inputs with no validation in the
  method, so every combination of decision outcomes, and therefore every basis path, is feasible.
- **TA5 - Valid inputs only.** Test inputs are valid values (amount > 0, balances ≥ 0). The method
  has no branches for invalid input, so such values would add no new path.
- **TA6 - Values away from boundaries.** Basis path testing exercises paths, not boundaries. Each
  test value is chosen well inside its condition (e.g. 50 for "amount < 100") so that a test
  exposes faults on its path, not threshold faults; the thresholds were the subject of
  Assignment 1.
- **TA7 - Oracle.** The expected fee is `amount × rate`, where the rate comes from the Assignment 2
  specification, converted from a percentage to a fraction (0.1% = 0.001). Fees are compared with
  a tolerance of 1e-9 to allow for floating-point rounding.

### Control-flow graph

Each node is named by its line number in `src/main/java/bank/utils/FeesCalculator.java`.
Comments, blank lines and closing braces are not nodes. Line 77 (`double feePercentage;`) only
declares a variable, so it is part of the entry node 75. `fp` stands for `feePercentage`.
`T` and `F` mark the true and false edges of each decision.
A zoomable copy of the same graph is in [transfer-cfg.svg](transfer-cfg.svg).

```mermaid
flowchart TD
    n75(["75: entry (amount, fromAccountBalance, toAccountBalance, student)"])
    n79{"79: student"}
    n80{"80: amount #60; 100"}
    n81{"81: fromAccountBalance #60; 1000"}
    n82{"82: toAccountBalance #60; 1000"}
    n88{"88: toAccountBalance #60; 1000"}
    n95{"95: fromAccountBalance #60; 1000"}
    n96{"96: toAccountBalance #60; 1000"}
    n102{"102: toAccountBalance #60; 1000"}
    n110{"110: amount #60; 100"}
    n111{"111: fromAccountBalance #60; 1000"}
    n112{"112: toAccountBalance #60; 1000"}
    n118{"118: toAccountBalance #60; 1000"}
    n125{"125: fromAccountBalance #60; 1000"}
    n126{"126: toAccountBalance #60; 1000"}
    n132{"132: toAccountBalance #60; 1000"}

    n83["83: fp = 0.01"]
    n85["85: fp = 0.005"]
    n89["89: fp = 0.05"]
    n91["91: fp = 0.025"]
    n97["97: fp = 0.005"]
    n99["99: fp = 0.0025"]
    n103["103: fp = 0.025"]
    n105["105: fp = 0.0125"]
    n113["113: fp = 0.02"]
    n115["115: fp = 0.01"]
    n119["119: fp = 0.1"]
    n121["121: fp = 0.05"]
    n127["127: fp = 0.01"]
    n129["129: fp = 0.005"]
    n133["133: fp = 0.05"]
    n135["135: fp = 0.055"]

    n142["142: fee = amount * fp"]
    n144(["144: return fee"])

    n75 --> n79
    n79 -- T --> n80
    n79 -- F --> n110

    n80 -- T --> n81
    n80 -- F --> n95
    n81 -- T --> n82
    n81 -- F --> n88
    n82 -- T --> n83
    n82 -- F --> n85
    n88 -- T --> n89
    n88 -- F --> n91
    n95 -- T --> n96
    n95 -- F --> n102
    n96 -- T --> n97
    n96 -- F --> n99
    n102 -- T --> n103
    n102 -- F --> n105

    n110 -- T --> n111
    n110 -- F --> n125
    n111 -- T --> n112
    n111 -- F --> n118
    n112 -- T --> n113
    n112 -- F --> n115
    n118 -- T --> n119
    n118 -- F --> n121
    n125 -- T --> n126
    n125 -- F --> n132
    n126 -- T --> n127
    n126 -- F --> n129
    n132 -- T --> n133
    n132 -- F --> n135

    n83 --> n142
    n85 --> n142
    n89 --> n142
    n91 --> n142
    n97 --> n142
    n99 --> n142
    n103 --> n142
    n105 --> n142
    n113 --> n142
    n115 --> n142
    n119 --> n142
    n121 --> n142
    n127 --> n142
    n129 --> n142
    n133 --> n142
    n135 --> n142

    n142 --> n144
```

### Cyclomatic complexity

| Method | Count | V(G) |
|---|---|---|
| Edges and nodes | E = 48, N = 34 (1 entry, 15 decisions, 16 assignments, 142, 144) | E − N + 2 = 48 − 34 + 2 = **16** |
| Decisions | P = 15 binary decisions (lines 79, 80, 81, 82, 88, 95, 96, 102, 110, 111, 112, 118, 125, 126, 132) | P + 1 = **16** |
| Regions | 15 enclosed regions (one per decision) + 1 outer region | **16** |

So there are 16 linearly independent basis paths. Because the decisions form a tree with no loops,
each basis path ends in a different assignment node (83 … 135).

### Basis paths (McCabe baseline method)

**Process.** We picked a baseline path and then flipped one decision at a time, keeping every other
decision as close to the path it was derived from as possible:

1. **Baseline P1** takes the true edge at every decision (student, amount < 100, from < 1000,
   to < 1000). Every path through this graph passes exactly four decisions, so no path is longer
   than another and the all-true path is as good a baseline as any.
2. **Flip each decision on the baseline** (79, 80, 81, 82) to get P2-P5.
3. **For each new path, flip the decisions on it that have not been flipped yet**, taking the true
   edge at any decision reached for the first time. This gives P6-P16.

A path is accepted only if it uses at least one edge that no earlier path used (the "New edge(s)"
column). Together the 16 paths cover all 48 edges, and the 16 × 48 path-edge incidence matrix has
rank 16, so the set is linearly independent and its size equals V(G).

| Path | Derived from | Flipped decision | Decision outcomes | Node sequence | New edge(s) | Ends at |
|---|---|---|---|---|---|---|
| P1 | baseline | - | 79T, 80T, 81T, 82T | 75-79-80-81-82-83-142-144 | 75→79, 79→80, 80→81, 81→82, 82→83, 83→142, 142→144 | 83 |
| P2 | P1 | 79 (student) → F | 79F, 110T, 111T, 112T | 75-79-110-111-112-113-142-144 | 79→110, 110→111, 111→112, 112→113, 113→142 | 113 |
| P3 | P1 | 80 (amount < 100) → F | 79T, 80F, 95T, 96T | 75-79-80-95-96-97-142-144 | 80→95, 95→96, 96→97, 97→142 | 97 |
| P4 | P1 | 81 (from < 1000) → F | 79T, 80T, 81F, 88T | 75-79-80-81-88-89-142-144 | 81→88, 88→89, 89→142 | 89 |
| P5 | P1 | 82 (to < 1000) → F | 79T, 80T, 81T, 82F | 75-79-80-81-82-85-142-144 | 82→85, 85→142 | 85 |
| P6 | P4 | 88 (to < 1000) → F | 79T, 80T, 81F, 88F | 75-79-80-81-88-91-142-144 | 88→91, 91→142 | 91 |
| P7 | P3 | 95 (from < 1000) → F | 79T, 80F, 95F, 102T | 75-79-80-95-102-103-142-144 | 95→102, 102→103, 103→142 | 103 |
| P8 | P3 | 96 (to < 1000) → F | 79T, 80F, 95T, 96F | 75-79-80-95-96-99-142-144 | 96→99, 99→142 | 99 |
| P9 | P7 | 102 (to < 1000) → F | 79T, 80F, 95F, 102F | 75-79-80-95-102-105-142-144 | 102→105, 105→142 | 105 |
| P10 | P2 | 110 (amount < 100) → F | 79F, 110F, 125T, 126T | 75-79-110-125-126-127-142-144 | 110→125, 125→126, 126→127, 127→142 | 127 |
| P11 | P2 | 111 (from < 1000) → F | 79F, 110T, 111F, 118T | 75-79-110-111-118-119-142-144 | 111→118, 118→119, 119→142 | 119 |
| P12 | P2 | 112 (to < 1000) → F | 79F, 110T, 111T, 112F | 75-79-110-111-112-115-142-144 | 112→115, 115→142 | 115 |
| P13 | P11 | 118 (to < 1000) → F | 79F, 110T, 111F, 118F | 75-79-110-111-118-121-142-144 | 118→121, 121→142 | 121 |
| P14 | P10 | 125 (from < 1000) → F | 79F, 110F, 125F, 132T | 75-79-110-125-132-133-142-144 | 125→132, 132→133, 133→142 | 133 |
| P15 | P10 | 126 (to < 1000) → F | 79F, 110F, 125T, 126F | 75-79-110-125-126-129-142-144 | 126→129, 129→142 | 129 |
| P16 | P14 | 132 (to < 1000) → F | 79F, 110F, 125F, 132F | 75-79-110-125-132-135-142-144 | 132→135, 135→142 | 135 |

`from` = `fromAccountBalance`, `to` = `toAccountBalance`.

### Path conditions

The input conditions that drive execution down each path. Every path is feasible: the four inputs
are independent parameters, so any combination of outcomes can be produced. Test values for each
path (task T3) are chosen from these conditions; expected fees come from the A2 specification,
not from the code.

| Path | student | amount | fromAccountBalance | toAccountBalance |
|---|---|---|---|---|
| P1 | true | < 100 | < 1000 | < 1000 |
| P2 | false | < 100 | < 1000 | < 1000 |
| P3 | true | ≥ 100 | < 1000 | < 1000 |
| P4 | true | < 100 | ≥ 1000 | < 1000 |
| P5 | true | < 100 | < 1000 | ≥ 1000 |
| P6 | true | < 100 | ≥ 1000 | ≥ 1000 |
| P7 | true | ≥ 100 | ≥ 1000 | < 1000 |
| P8 | true | ≥ 100 | < 1000 | ≥ 1000 |
| P9 | true | ≥ 100 | ≥ 1000 | ≥ 1000 |
| P10 | false | ≥ 100 | < 1000 | < 1000 |
| P11 | false | < 100 | ≥ 1000 | < 1000 |
| P12 | false | < 100 | < 1000 | ≥ 1000 |
| P13 | false | < 100 | ≥ 1000 | ≥ 1000 |
| P14 | false | ≥ 100 | ≥ 1000 | < 1000 |
| P15 | false | ≥ 100 | < 1000 | ≥ 1000 |
| P16 | false | ≥ 100 | ≥ 1000 | ≥ 1000 |

### Specification rule for each path

Each path's conditions match exactly one rule of the Assignment 2 transfer specification
("The business logic: fee requirements", Transfer). That rule gives the expected rate used as the
test oracle (TA7).

| Path | Specification rule | Expected rate |
|---|---|---|
| P1 | Student, amount < $100, from < $1,000, to < $1,000 | 0.1% (0.001) |
| P2 | Non-student, amount < $100, from < $1,000, to < $1,000 | 0.2% (0.002) |
| P3 | Student, amount ≥ $100, from < $1,000, to < $1,000 | 0.05% (0.0005) |
| P4 | Student, amount < $100, from ≥ $1,000, to < $1,000 | 0.5% (0.005) |
| P5 | Student, amount < $100, from < $1,000, to ≥ $1,000 | 0.05% (0.0005) |
| P6 | Student, amount < $100, from ≥ $1,000, to ≥ $1,000 | 0.25% (0.0025) |
| P7 | Student, amount ≥ $100, from ≥ $1,000, to < $1,000 | 0.25% (0.0025) |
| P8 | Student, amount ≥ $100, from < $1,000, to ≥ $1,000 | 0.025% (0.00025) |
| P9 | Student, amount ≥ $100, from ≥ $1,000, to ≥ $1,000 | 0.125% (0.00125) |
| P10 | Non-student, amount ≥ $100, from < $1,000, to < $1,000 | 0.1% (0.001) |
| P11 | Non-student, amount < $100, from ≥ $1,000, to < $1,000 | 1% (0.01) |
| P12 | Non-student, amount < $100, from < $1,000, to ≥ $1,000 | 0.1% (0.001) |
| P13 | Non-student, amount < $100, from ≥ $1,000, to ≥ $1,000 | 0.5% (0.005) |
| P14 | Non-student, amount ≥ $100, from ≥ $1,000, to < $1,000 | 0.5% (0.005) |
| P15 | Non-student, amount ≥ $100, from < $1,000, to ≥ $1,000 | 0.05% (0.0005) |
| P16 | Non-student, amount ≥ $100, from ≥ $1,000, to ≥ $1,000 | 0.25% (0.0025) |

### Test cases

One test case per basis path (TC-T01 for P1 … TC-T16 for P16), with concrete inputs taken from the
path conditions above and the expected fee computed from the rate in the specification table.
This table is added with the transfer JUnit tests (tasks T3 and T4).
