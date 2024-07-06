# multisub

This script submits a job to the first available partition from a list of partitions. It is useful when you have a job that can run on multiple partitions and you want to submit it to the first available partition.

## Setup

1. Clone this repository:

```bash
git clone https://github.com/maouw/slurm-submit-to-multiple
```

2. Add the `multisub` script to your PATH:

```bash
echo "export PATH=\"$PATH:$PWD/slurm-submit-to-multiple"\" >> ~/.bashrc
```

## Usage

```
multisub [--help] [SBATCH_OPTIONS] -- [args to script...]
```

Submit a job to the first available partition from a list of partitions.

All arguments are passed to sbatch except for those after the double dash (`--`), which are passed to the batch script.

You must provide a valid SLURM batch script as the first argument before `--`. Only bash or sh scripts are supported. The script must contain a shebang line with the path to the bash or sh interpreter. Arguments after `--` are passed to the batch script.

To specify the partitions to submit the job to, use the `-p` or `--partition` option. If the `-p` or `--partition` option is not provided, multisub will use the partitions specified in the `SBATCH_PARTITION` environment variable. The partitions must be comma-separated. `multisub` will pass each partition to sbatch separately.

If you would like to specify multiple account/partition pairs, you can use the format `account:partition` in the `-p` or `--partition` option. Partitions specified without an account prefix will use the account specified in the `--account` option or the `SBATCH_ACCOUNT` environment variable. For example, `--account account1 --partition partition1,partition2,account2:partition3` will submit the job to `partition1` with `account1`, `partition2` with `account1`, and `partition3` with `account2`.

If the `-J` or `--job-name` option is not provided, `multisub` will generate a unique job name.

To see the full list of options, run `multisub --help`.

### Examples

```bash
# Submit a job to the first available partition from a list of partitions:
multisub --account escience gpu-a40,gpu-rtx6k -- my-batch-script.sh arg1 arg2

# Submit a job to partitions using different accounts:
multisub --partition=escience:gpu-a40,psych:cpu-g2-memx2 -- my-batch-script.sh arg1 arg2

# Submit a job to the first available partition from a list of partitions with one partition using a different account:
multisub --account escience --partition=gpu-rtx6k,gpu-a40,psych:cpu-g2-mem2x -- my-batch-script.sh arg1 arg2

# Submit a job to the first available partition from a list of partitions with a custom job name:
multisub --partition=cpu,cpu-rtx6k --job-name=my-job -- my-batch-script.sh arg1 arg2
```

## Caveats

- This script is provided as-is without any guarantees. Use at your own risk.
- This script is not officially supported by SLURM or any other organization.
- Using this script may degrade the experience for other users of the cluster. Please use it responsibly and keep an eye on your jobs.
- In the case of partitions with overlapping nodes and/or resources, this script might block or use the resources suboptimally.
- This script does not support heterogenous SLURM jobs.
- Job arrays might not work as expected.
- This script might not work with all SLURM versions or configurations.
