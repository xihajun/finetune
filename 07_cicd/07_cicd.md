# Workflow

## GitHub Actions Workflow Tutorial: "Check Resources"

This GitHub Actions workflow, named "Check Resources," is designed to automate the process of testing machine performance and resource availability for different configurations. Here's a breakdown of its components:

## Workflow Trigger
- **On Push**: Activates on a push to the `cicd_test` branch.
- **Workflow Dispatch**: Allows manual triggering with an input option to select a specific machine or all machines.

## Jobs Overview
The workflow consists of several jobs, each performing specific tasks:

1. **`prepare-matrix`**: Sets up a matrix of machines to test.
2. **`check-runner-availability`**: Ensures the specified runner machine is available.
3. **`get-loadgen-target-qps`**: Calculates the Queries Per Second (QPS) matrix for load generation.
4. **`check-resources-and-run`**: Checks resource availability and runs tests.
5. **`summary`**: Summarizes and comments on the test results.


## Workflow Execution
- The workflow follows a sequential and conditional execution pattern, ensuring that each step is dependent on the success of the previous steps.
- It efficiently manages resources, like Docker containers, and ensures clean-up after tests.
- The results from the testing are saved, uploaded, and summarized for easy review.

This workflow demonstrates a comprehensive approach to testing and resource management, leveraging GitHub Actions' capabilities to automate complex CI/CD processes.



Certainly! Here's the breakdown of the GitHub Actions workflow "Check Resources" along with the relevant code snippets for each job:

### Job 1: `prepare-matrix`

This job sets up a matrix of machines to test, determining which machines should be included based on the trigger event.

- **Runs On**: The latest Ubuntu runner.
- **Purpose**: Dynamically creates a matrix of machines based on the trigger event.
- **Steps**:
  - Checks if the workflow is triggered by a push or a manual dispatch and sets the machine matrix accordingly.

??? code
    ```yaml
    prepare-matrix:
      runs-on: ubuntu-latest
      outputs:
        matrix: ${{ steps.set-matrix.outputs.matrix }}
      steps:
      - name: Set Running Machine Matrix
        id: set-matrix
        run: |
          if [[ "${{ github.event_name }}" == "push" ]]; then
            echo "matrix=$MACHINE_LIST" >> $GITHUB_OUTPUT
          elif [[ "${{ github.event.inputs.machine }}" == "all" ]]; then
            echo "matrix=$MACHINE_LIST" >> $GITHUB_OUTPUT
          else
            echo "matrix=[\"${{ github.event.inputs.machine }}\"]" >> $GITHUB_OUTPUT
          fi
    ```

### Job 2: `check-runner-availability`

Dependent on `prepare-matrix`, this job ensures the specified runner machine is available.


- **Dependency**: Needs `prepare-matrix` to complete first.
- **Runs On**: The machine specified in the matrix.
- **Timeout**: Set to 2 minutes.
- **Purpose**: Verifies that the selected runner is available.
- **Steps**:
  - Prints a confirmation message once the runner is assigned.

??? code
    ```yaml
    check-runner-availability:
      needs: prepare-matrix
      runs-on: ${{ matrix.machine }}
      timeout-minutes: 2
      strategy:
        matrix:
          machine: ${{fromJson(needs.prepare-matrix.outputs.matrix)}}
      steps:
      - name: Wait for runner
        run: |
          echo "Runner ${{ matrix.machine }} assigned and job started."
    ```

### Job 3: `get-loadgen-target-qps`

This job calculates the Queries Per Second (QPS) matrix for load generation, depending on `check-runner-availability`.



- **Dependency**: Requires `check-runner-availability`.
- **Runs On**: Ubuntu latest.
- **Purpose**: Computes a range of QPS values for testing.
- **Steps**:
  - Runs a script to generate a sequence of QPS values and sets it as an output.

??? code
    ```yaml
    get-loadgen-target-qps:
      needs: check-runner-availability
      runs-on: ubuntu-latest
      outputs:
        qps_matrix: ${{ steps.set-qps.outputs.qps_matrix }}
      steps:
      - name: Calculate QPS Matrix
        id: set-qps
        run: |
          current_qps=1000  # Replace with actual value
          one_percent=$((current_qps / 100))
          qps_matrix=$(seq $((current_qps - one_percent)) $one_percent $((current_qps + one_percent)) | tr '\n' ',' | sed 's/,$//')
          qps_matrix="[$qps_matrix]"
          echo "::set-output name=qps_matrix::$qps_matrix"
    ```

### Job 4: `check-resources-and-run`

After `check-runner-availability` and `get-loadgen-target-qps`, this job checks resource availability and runs tests.

- **Dependencies**: Waits for `check-runner-availability` and `get-loadgen-target-qps`.
- **Runs On**: The machine specified in the matrix.
- **Strategy**: Defines a matrix with multiple parameters like machine, QPS, framework, and device.
- **Steps**:
  - **Code Checkout**: Checks out the repository code.
  - **Docker Container Management**: Manages Docker containers based on their availability.
  - **Resource Checking**: Determines the number of devices and checks card availability.
  - **Experiment Execution**: Runs accuracy and performance tests if resources are available.
  - **Docker Cleanup**: Stops and removes the Docker container.
  - **Result Handling**: Prints, saves, and uploads test results.

??? code
    ```yaml
    check-resources-and-run:
      needs: [check-runner-availability, get-loadgen-target-qps]
      runs-on: ${{ matrix.machine }}
      strategy:
        matrix:
          machine: ${{fromJson(needs.check-runner-availability.outputs.matrix)}}
          loadgen_target_qps: ${{fromJson(needs.get-loadgen-target-qps.outputs.qps_matrix)}}
          framework: ['kilt']
          device: ['qaic']
          loadgen_scenario: ['Offline', 'Server']
          config: 
            - {model: 'resnet50', benchmark_type: 'image_classifier', container: 'krai/axs.resnet50', special_params: ''}
      steps:
      # Steps for checking out code, managing Docker container, checking resources, running experiments, handling results, etc.
    ```

### Job 5: `summary`

The final job summarizes and comments on the test results.


- **Dependency**: Needs `check-resources-and-run` to complete.
- **Runs On**: Ubuntu latest.
- **Purpose**: Creates a summary of the test results.
- **Steps**:
  - Checks out the code, downloads all artifacts, generates a summary, and comments on an issue with the results.

??? code
    ```yaml
    summary:
      needs: check-resources-and-run
      runs-on: ubuntu-latest
      steps:
      - name: Check out code
        uses: actions/checkout@v3
      - name: Download all artifacts
        uses: actions/download-artifact@v2
      - name: Execute Summary Script
        run: |
          chmod +x .github/scripts/summary/generate_summary.sh
          ./.github/scripts/summary/generate_summary.sh
      - name: Comment on the issue
        uses: peter-evans/create-or-update-comment@v3
        with:
          issue-number: 115
          body-path: 'table.md'
    ```
