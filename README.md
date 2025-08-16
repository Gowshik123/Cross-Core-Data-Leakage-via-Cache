# Cross-Core-Data-Leakage-via-Cache
This involves implementation of Cache based side-channel implementations between different cores of a processor. Different types of data transmission has been implemented like Flush-Reload and Cache Occupancy based attacks. These types of communication allows low moderate data-rates upto 8-10Kbps with accuracy above 90%


# CS773 Assignment 1

Refer [this](https://docs.google.com/document/d/1a77P4xrjjIW19FLUkyICbp5-0GycmheJxvPIxOFUmTA/edit?usp=sharing) link to understand the problem statement


## Submission Details
Gowshik L - 22b1297
Praveen Prajapat - 22b3931
Anirudh KR Jangid - 22b1251


## Task 2A
### Approach

Shared Memory - Create a shared memory object using shm_open, communication is operated on a memory address pointing to a variable(int). If 1 is being sent then the variable is called.
Synchronization - Receiver is always up and running, it accesses the memory address once in a specific cycle interval. Sender also transmits in the same frequency. There is a start and stop sequence within which the data is being communicated.

Calibration - Sometimes, the cache_threshold that is given in calibration code is not matching.
Accuracy improvement - There is variation in accuracy with different machine, so we are transmitting each data twice if the accuracy. The data in which difference in threshold and access time is larger, that data is selected.

Circular buffer based start sequence detector - When accuracy in a specific machine is less, then there are chances of start sequence getting missed. In that cases even if 7 out of 8 bits in the start sequence is matching while both the 1st and 8th bit are correct, then the start sequence is accepted.



### Results
- **Accuracy:** 100%
- **Bandwidth:** 2964 bits/second

**Demo Video** - https://drive.google.com/file/d/1pYbToIRRJA_g4xvvdUmUGU_TozS_wPq3/view?usp=sharing

Note - We could get same accuracy at higher bandwidth also, but above this bandwidth the transmission was not reliable. Sometimes we got completely correct message and sometimes with a lot of errors.

You can also mention accuracy vs bandwidths that you observed while tuning.

## Task 2B
### Approach
[Describe your approach for Task 2B. Mention any techniques, or optimizations used.]

Arrays to thrash LLC cache - In both sender and receiver, we defined two arrays (Array name - buffer) of size equal to LLC size (8MB in our case) seperately in both files

Global clock - In both sender and receiver, we've put a clock signal of 4ms (value got by tuning). The way this clock signal works is by making a global flag (timer_triggered) = 1 every time 4ms passes and this flag is made 0 by us in loop for sending message bits (which keep on checking for value of the flag and makes it zero if found 1)

Logic to send bits - Receiver keeps on accessing its buffer array (8MB size) continuously and keeps a track of no of times complete array is accessed in 4ms (counter), In the mean time, Sender will access its buffer array (8MB size) continuously for 4ms if bit is 1, otherwise don't access 

Result of doing it by above methods is that when only receiver is accessing the L3 cache it can access complete cache around 4 to 5 times in 4ms, but when both processes (sender and receiver) are concurrently accessing the L3 cache, this count drops to either 2 or 3 in 4ms.
based on threshold = 4, we are differentiating whether both processes are acccessing or just sender. 
If both are accessing implies bit is one, and if only sender is accessing bit is 0.   


### Results
- **Accuracy:** 99.1%
- **Bandwidth:** 250 bits/second


**Demo video** - https://drive.google.com/file/d/1V9N-t2m8y0RFw7WK80ml2JqkFb4SUmLQ/view?usp=sharing

You can also mention accuracy vs bandwidths that you observed while tuning.
--One other set of parameter (cycle time = 10ms, threshold = 7) was giving 100 bits/second and 99.7% accuracy


## Task 3
### Approach

Most of the things are similar to task 2A, there is some issue on transmitting more than 600 bytes of code. So we resynchronised the receiver and transmitter for every 600 bytes sent. the check_accuracy function was giving good accuracy for received message (more than 90%) but reconstructed Image was not good.

### Changes from Previous Tasks
[Explain any changes made in this task compared to Task 2A/2B.]

### Results
#### Image Transmission
We tried on two images including one heart image
- **Sent Image:** - [`task3a/sent_image.jpg`](task3a/sent_image.jpg)
                 - [`task3a/sent_heart.jpg`](task3a/sent_heart.jpg)
- **Received Image:** - [`task3a/recovered_image.jpg`](task3a/recovered_image.jpeg)
                    [`task3a/recovered_heart.jpg`](task3a/recovered_heart.jpeg)
- **Time taken:** 1. 32.87 second, 2. 131.45 seconds


**Note** - We have tried with low resolution image, as original one was very high resolution and taking more time


#### Audio Transmission
- **Sent Audio:** *(Provide the audio file name or attach the audio)*
- **Received Audio:** *(Provide the audio file name or attach the audio)*
- **Time taken:** How long did it take for the transmission.


## Plagiarism Checklist
Your answers should be written in a new line below each bullet.

1. Have you strictly adhered to the submission guidelines?
    Yes

2. Have you received or provided any assistance for this assignment beyond public discussions with peers/TAs?
    Yes

3. If you answered "yes" to the previous question, please specify what part of code you've taken and from whom you've taken it.
    Task 2 part A, Circular buffer based idea to find the start sequence when accuracy is low (source: ChatGPT)
    Task 2 part A, To setup a shared memory we have referred this source (source: https://www.qnx.com/developers/docs/8.0/com.qnx.doc.neutrino.lib_ref/topic/s/shm_open.html)
    Task 2 part B, The code to make global clock using interrups and SIGALRM (source: https://aljensencprogramming.wordpress.com/2014/05/09/introduction-to-signals-using-alarm-in-linux-c/)
    Task 3 part A, We've used ChatGPT for python files to convert image to binary data and hex files and vice versa and also to clean the received data and garbage ascii codes
    


## Directory Structure
Folders - `task2a`, `task2b`, `task3a` and optionally `task3b`. Each of these directories should contain:

1. `sender.c`
2. `receiver.c`
3. `utils.c`
4. `utils.h`
5. `MakeFile`
6. Additional relevant files

If the implementation for `task3a` and `task3b` is the same, put the code in the folder `task3a`. Apart from the above folders, there should be a completely filled README file.


Your submission should be `zip` file with \<roll-number\>.zip. Only one team member should submit, else there will be penalties.


