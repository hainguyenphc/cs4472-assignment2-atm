# CS 4472 Assignment 2: White-Box Testing

## Transfer fee (`FeesCalculator.calculateTransferFee`): basis path testing

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
