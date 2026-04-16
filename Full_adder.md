Circuit that adds three inputs and produces two outputs.

```
$a = $rand_a[0];
$b = $rand_b[0];
$cin = $rand_cin[0];

$sum = $a ^ $b ^ $cin;

$cout = ($a & $b) | ($cin & ($a ^ $b));

*passed = *cyc_cnt > 10;
```
