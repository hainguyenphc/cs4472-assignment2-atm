Pseudocode of the Assignment 2 fee specification. It is the oracle for expected values only;
control-flow graphs, slices and DU-paths are computed from `bank.utils.FeesCalculator` itself.

## withdrawal pseudocode
```
if (client is student) {
	if (today is weekend) {
		fee = 0;
	}
	else {
		fee = 0.1% of withdrawn amount;
	}
}
else {
	if (balance < 1000) {
		fee = 0.2% of withdrawn amount;
	}
	else if (balance >= 1000 and balance < 10000) {
		fee = 0.1% of withdrawn amount;
	}
	else if (balance > 10000) {
		fee = 0;
	}
	// balance == 10000 is not covered by the specification
}
```

## deposit pseudocode
```
if (client is student) {
	if (amount > 100) {
		if (balance > 1000) {
			fee = 1% of deposited amount;
		}
		else {
			fee = 0.5% of deposited amount;
		}
	}
	else { // amount <= 100
		if (balance > 5000) {
			fee = 0.5% of deposited amount;
		}
		else {
			fee = 0;
		}
	}
}
else {
	if (amount > 500) {
		if (balance > 5000) {
			fee = 1% of deposited amount;
		}
		else {
			fee = 0.5% of deposited amount;
		}
	}
	else { // amount <= 500
		if (balance > 10000) {
			fee = 0.5% of deposited amount;
		}
		else {
			fee = 0;
		}
	}
}
```

## transfer pseudocode
```
if (client is student) {
	if (amount < 100) {
		if (fromBalance < 1000) {
			if (toBalance < 1000) {
				fee = 0.1% of transferred amount;
			}
			else {
				fee = 0.05% of transferred amount;
			}
		}
		else { // fromBalance >= 1000
			if (toBalance < 1000) {
				fee = 0.5% of transferred amount;
			}
			else {
				fee = 0.25% of transferred amount;
			}
		}
	}
	else { // amount >= 100
		if (fromBalance < 1000) {
			if (toBalance < 1000) {
				fee = 0.05% of transferred amount;
			}
			else {
				fee = 0.025% of transferred amount;
			}
		}
		else { // fromBalance >= 1000
			if (toBalance < 1000) {
				fee = 0.25% of transferred amount;
			}
			else {
				fee = 0.125% of transferred amount;
			}
		}
	}
}
else {
	if (amount < 100) {
		if (fromBalance < 1000) {
			if (toBalance < 1000) {
				fee = 0.2% of transferred amount;
			}
			else {
				fee = 0.1% of transferred amount;
			}
		}
		else { // fromBalance >= 1000
			if (toBalance < 1000) {
				fee = 1% of transferred amount;
			}
			else {
				fee = 0.5% of transferred amount;
			}
		}
	}
	else { // amount >= 100
		if (fromBalance < 1000) {
			if (toBalance < 1000) {
				fee = 0.1% of transferred amount;
			}
			else {
				fee = 0.05% of transferred amount;
			}
		}
		else { // fromBalance >= 1000
			if (toBalance < 1000) {
				fee = 0.5% of transferred amount;
			}
			else {
				fee = 0.25% of transferred amount;
			}
		}
	}
}
```
