# Identifying mutations in Next-Generation Sequence (NGS) reads

## Title & Purpose
This Colab notebook compares 6,000 aligned sequencing reads from fission yeast against a 200-nt reference to find mismatches, and tests whether samples treated with a genotoxic agent show more mutations, or mutation hotspots, than untreated controls.

## How to run the program
Open the notebook in Google Colab ([open in Colab](https://colab.research.google.com/github/Rudie-K/PFL_Project1/blob/main/C7147_Project_1_assessment.ipynb)) and choose **Runtime → Run all**. Cells must run in order, because later steps reuse earlier functions.

## Inputs and outputs
**Inputs:** `reference.txt` (200-nt reference) and `reads.csv` (read_id, sample, group, sequence), downloaded automatically by the setup cells.

**Outputs:** `analyse_reads` returns every read's validation status and mismatches; `summarise_samples` returns per-sample counts and prints, for each group, a results table and a substitution table; `compare_positions` returns and prints a Welch's t-test for the three most-mutated positions.

## Code design
- **Validation:** `validate_read` checks length on the original read, not the upper-cased one, because upper-casing can change length (`"ß"` becomes `"SS"`). Whitespace counts as invalid; I considered stripping it, as Lab 4's `clean_dna` does, but chose to report damaged reads rather than silently repair them.
- **One tie rule:** `find_top_positions` sorts by position, then by count; Python's sort keeps tied items in order, so tied positions stay earliest-first. Steps 4 and 5 both reuse it, so the rule cannot drift between steps.
- **Read length:** `compare_positions` is not given the reference, so the combined count list runs to the highest mismatched position. I considered adding a `read_length` parameter but kept the required signature; later positions have zero counts and cannot change the result.

## Testing
- **Input unchanged:** a deliberately buggy `analyse_reads` that edited the input reads still passed the provided test, but my check against a copy of the original reads caught it.
- **Ties:** `find_top_positions({9: 2, 2: 2, 5: 2, 1: 1})` must return `[2, 5, 9]`.
- **Counting:** a read with two mismatches must add two events but one mismatched read, with percentages using valid reads only (66.67%, not 50%).
