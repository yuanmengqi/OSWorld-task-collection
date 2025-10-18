# OSWorld-v2 Task Implementation Guide

## 1. Task Selection Criteria

We are looking for **real, complex computer-use tasks**.  
Please feel free to propose tasks that:
- Take **more than 50 steps or 10 minutes** to complete, or  
- You have attempted with existing agents and found to be **inaccessible for the agent** to complete.

These tasks can come from your **daily life, study, or work**.  
Whenever you find yourself thinking, *“This task takes too much time — I wish an agent could handle it,”* it may be a valuable task to consider.  
If the task also has **practical or business value**, that would be especially appreciated.

When designing your task, kindly keep the following points in mind:

1. **Avoid Ambiguity:**  
   Please ensure clear and specific instructions to avoid any confusion. For example, instead of:  
   > “Schedule a meeting with XXX at 3 PM on Jan 1, 2025,”  
   kindly specify **where** the meeting should occur — e.g., “Schedule the meeting on Google Calendar” or “Create a Zoom meeting.”

2. **Ensure a Unique Result:**  
   The result should be **objectively verifiable**.  
   For instance, a task like “Find a route from A to B” may be unsuitable because there can be many valid paths.

3. **Avoid Time-Sensitive Tasks:**  
   Tasks that rely on rapidly changing data (e.g., “Find the top 10 AI universities in the USA on CSRankings”) may be unstable for evaluation purposes.

4. **Evaluation Method:**  
   We recommend **function-based evaluation** where possible. However, **model-based evaluation** is also supported if it is stable and reproducible.  
   For example, model evaluation is appropriate for verifying *“whether the inserted text is centered in the image”*, but may not be suitable for more subjective judgments such as *“how good is this summary.”*

---

## 2. Environment Setup

The runtime environment is built on the **OSWorld framework**, primarily operating within **AWS evaluation environments**.  
For detailed configuration steps, please refer to: PUBLIC_EVALUATION_GUIDELINE

We also support **Docker** and **VMware** environments.  
Further setup details can be found in 

---

## 3. Task Implementation Workflow

A task in OSWorld typically involves four stages:

1. **Environment Setup** – Preparing the initial Ubuntu environment for the agent.  
2. **Agent Execution** – The agent performs the required actions to complete the task.  
3. **Post-Processing** – Save system states or results after execution.  
4. **Evaluation** – Compare the results with the ground truth using function or model evaluation.

### File Preparation

All task-related files should be stored in the following shared Drive:  
🔗 [Google Drive Folder](https://drive.google.com/drive/folders/1N4f5mTmYtVLC2uEATKk6eD_ZTKoY96gN?usp=sharing)

Each task should have its own folder, named **task_xxx**, which includes:

- **Initial files** used during the setup stage  
- **Ground truth files** used in the evaluation stage

### Code Implementation

The OSWorld-v2 repository is located at:  
🔗 [GitHub – OSWorld-V2](https://github.com/yuanmengqi/OSWorld-V2.git)

You will need to implement two main components:

1. **Task Configuration File**  
   Stored in:  `evaluation_examples/examples`
    The configuration file should include:
    - The task ID and instruction
    - The setup steps (config)
    - Post-processing and evaluation settings

2. **Evaluation Functions**  
Please implement or reference the necessary functions in the following directories:`/home/ubuntu/OSWorld-v2/desktop_env/evaluators/getters` and `/home/ubuntu/OSWorld-v2/desktop_env/evaluators/metrics`

---

## 4. Example Task

### Example 1

#### Step 1: Write the Task Instruction

> “The file 'transcript_Hua_Li.pdf' contains the courses I have already completed, while 'Computer_Science_and_Technology_Program_Curriculum_Class_of_2025.pdf' outlines my program requirements. Please calculate the remaining credits for each module in the 'Compulsory Courses' and 'Elective Courses' sections. For the 'Core General Education Courses' module, the remaining credits should be calculated as 160 total credits minus the credits I have already earned and the remaining credits for other modules. Fill in the 'Remaining_Course.xlsx' file accordingly. Additionally, list the names of any compulsory courses I have not taken in the empty spaces under 'Remaining Courses.'”

#### Step 2: Prepare Files

- **Initial files**:
- `Computer_Science_and_Technology_Program_Curriculum_Class_of_2025.pdf`
- `transcripts_Hua_Li.pdf`
- `Remaining_Course.xlsx`
- **Ground truth**:
- `Remaining_Course_gt.xlsx`

Please upload these files to the appropriate folder in Google Drive.

#### Step 3: Create the Task Config

Example path: `/home/ubuntu/OSWorld-v2/evaluation_examples/new_tasks/example.json`

Key components include:

- **id**: Unique task identifier (also added to `/evaluation_examples/test_new.json` under `new_tasks`).
- **instruction**: The task description.
- **config**: Setup steps — e.g., download initial files and open them in LibreOffice.
- **evaluator**: Defines post-processing, evaluation function, ground truth, and comparison options.

(See the full JSON example in the original text; structure remains unchanged.)

#### Step 4: Define or Reference Evaluation Functions

Functions are located in:

- `/desktop_env/evaluators/getters` — for retrieving data (e.g., files)
- `/desktop_env/evaluators/metrics` — for comparing outputs

Common examples:
- `get_cloud_file` – download a file from cloud storage.
- `get_vm_file` – read a file from the VM.
- `compare_table` – compare two Excel tables cell by cell.

If you define any **new functions**, please remember to import them in `__init__.py`.

#### Step 5: Validate Your Task

You can manually test your task setup and evaluation using the script: `/home/ubuntu/OSWorld-v2/manual_examine.py`

Example command:
```
python manual_examine.py \
    --headless \
    --observation_type screenshot \
    --result_dir ./results_new_tasks \
    --test_all_meta_path evaluation_examples/test.json \
    --region us-east-1 \
    --domain new_tasks \
    --example_id '001' \
    --max_steps 3
```
After running the script, you will be provided with a VNC link in the terminal. You can access the virtual machine by logging in with the password:

osworld-public-evaluation


Once logged in, manually complete the task in the VNC environment, then press Enter to trigger the evaluation.
Finally, review the evaluation results to confirm that your setup and evaluation functions are working as expected.


