# goit-pyweb-hw-03

**TASK1**

***sort.py***

```
import os
import shutil
import sys
from concurrent.futures import ThreadPoolExecutor
from threading import Lock

lock = Lock()

def create_directory_if_not_existed(directory):
    """
    This function creates a directory if it does not exist. It uses a lock to ensure thread safety.

    Parameters:
    directory (str): The path of the directory to be created.

    Returns:
    None. The function creates the directory if it does not exist.
    """
    with lock:
        if not os.path.exists(directory):
            os.makedirs(directory)
    with lock:
        if not os.path.exists(directory):
            os.makedirs(directory)


def copy_file_to_target(file_path, target_root):
    """
    This function copies a file from the source directory to a target directory,
    creating the target directory if it does not exist.

    Parameters:
    file_path (str): The path of the file to be copied.
    target_root (str): The root directory where the file will be copied. The file will be
                       placed in a subdirectory based on its file extension.

    Returns:
    None. The function prints a message indicating the successful copy operation.
    """
    file_extension = file_path.split('.')[-1].lower()
    target_directory = os.path.join(target_root, file_extension)
    create_directory_if_not_existed(target_directory)
    shutil.copy(file_path, target_directory)
    print(f"File '{file_path}' copied to '{target_directory}'")


def process_directory(directory, target_root):
    with ThreadPoolExecutor( ) as executor:
        for root, dirs, files in os.walk(directory):
            for file in files:
                file_path = os.path.join(root, file)
                executor.submit(copy_file_to_target, file_path, target_root)
def main():
    if len(sys.argv) < 2:
        print("Usage: python file_processor.py <source_directory> <target_directory>")
        sys.exit(1)

    source_directory = sys.argv[1]
    target_directory = sys.argv[2] if len(sys.argv) > 2 else'dist'

    print(f"Source Directory: {source_directory}")
    print(f"Target Directory: {target_directory}")

    process_directory(source_directory, target_directory)
    print("Processing complete.")

if __name__ == "__main__": 
    main()

#CMD to run the script: python sort.py source_directory target_directory
```

**TASK 2**

**sync.py**
```
import time

def factorize(*numbers):
    result = []
    for number in numbers:
        divisors = []
        for i in range(1,number+1):
            if number % i == 0:
                divisors.append(i)
        result.append(divisors)
    return result

start_time = time.time()

a, b, c, d  = factorize(128, 255, 99999, 10651060)

end_time = time.time()
print(f'Execution time: {end_time - start_time} seconds')

assert a == [1, 2, 4, 8, 16, 32, 64, 128]
assert b == [1, 3, 5, 15, 17, 51, 85, 255]
assert c == [1, 3, 9, 41, 123, 271, 369, 813, 2439, 11111, 33333, 99999]
assert d == [1, 2, 4, 5, 7, 10, 14, 20, 28, 35, 70, 140, 76079, 152158, 304316, 380395, 532553, 760790, 1065106, 1521580, 2130212, 2662765, 5325530, 10651060]

print('All tests passed')
```

**parallel.py**
```
import time
from multiprocessing import Pool, cpu_count

def find_divisors(number):
    divisors = []
    for i in range(1, number+1):
        divisors.append(i)
    return divisors

def factorize_parallel(*numbers):
    with Pool(cpu_count()) as pool:
        results = pool.map(find_divisors, numbers)
    return results

start_time = time.time()

a, b, c, d  = factorize_parallel(128, 255, 99999, 10651060)

end_time = time.time()

print(f'Execution time: {end_time - start_time} seconds')

assert a == [1, 2, 4, 8, 16, 32, 64, 128]
assert b == [1, 3, 5, 15, 17, 51, 85, 255]
assert c == [1, 3, 9, 41, 123, 271, 369, 813, 2439, 11111, 33333, 99999]
assert d == [1, 2, 4, 5, 7, 10, 14, 20, 28, 35, 70, 140, 76079, 152158, 304316, 380395, 532553, 760790, 1065106, 1521580, 2130212, 2662765, 5325530, 10651060]

print('All tests passed')
```