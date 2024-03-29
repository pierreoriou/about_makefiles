# ABOUT MAKEFILES

## Variables

    NAME = fdf

- __NAME__ : This variable specifies the name of the executable file that will be generated when the program is compiled. In this case, it's set to fdf.

---

    SRCS = main.c \
           check_file_errors.c \
		   ...
           utils/get_tab_len.c \
           utils/free_close_exit.c

- __SRCS__ : This variable contains a list of the source files required to build the program. These files are the .c files that contain the actual code for the program.

---

    OBJS_DIR = obj

- __OBJS_DIR__ : This variable specifies the directory where the object files (.o files) will be stored. It's set to obj.

---

    OBJS = ${addprefix ${OBJS_DIR}/,${SRCS:.c=.o}}

- __OBJS__ : This variable specifies the object files that need to be created from the source files. It's created using the addprefix function to prepend the OBJS_DIR to each object file. The ${SRCS:.c=.o} part replaces the .c extension of each source file with .o, indicating that object files should be created from the corresponding source files.

---

    LIBFT_PATH = ./Libft
    LIBFT_NAME = libft.a
    MINILIBX_PATH = ./Minilibx
    MINILIBX_NAME = libmlx.a

- __LIBFT_PATH__ : This variable specifies the path to the directory containing the libft.a library.
- __LIBFT_NAME__ : This variable specifies the name of the libft.a library file.
- __MINILIBX_PATH__ : This variable specifies the path to the directory containing the libmlx.a library.
- __MINILIBX_NAME__ : This variable specifies the name of the libmlx.a library file.

---

    CC = gcc

- __CC__ : This variable specifies the compiler to be used. In this case, it's set to gcc.

---

    CFLAGS = -Wall -Wextra -Werror

- __CFLAGS__ : This variable specifies the compiler flags to be used during compilation. The flags -Wall -Wextra -Werror enable common warnings and treat warnings as errors, ensuring stricter error checking during compilation.

---

    INCLUDES = fdf.h

- __INCLUDES__ : This variable specifies the header file(s) needed for compilation. In this case, it's set to fdf.h.

---

## Rules


    ${OBJS_DIR}/%.o: %.c ${INCLUDES}
    @mkdir -p ${dir $@}
    ${CC} ${CFLAGS} -c $< -o $@

- ```${OBJS_DIR}/%.o:%.c ${INCLUDES}``` : This is a pattern rule for creating object files from source files. It specifies that each .c file in the source directory should be compiled into a corresponding .o file in the object directory. It also specifies ```${INCLUDES}``` as a prerequisite, ensuring that any changes to header files trigger recompilation.
- ```@mkdir -p ${dir $@}``` :
	- This command creates the ```${OBJS_DIR}``` directory if it doesn't exist. The ```${dir $@}``` expression extracts the directory part of the target file path (```$@```).
	- ```@``` : This symbol tells Make to suppress the echoing of the command to the terminal. It ensures that the command is executed silently without being displayed.
	- ```mkdir -p``` : This is the shell command to create directories. The ```-p``` option tells mkdir to create the specified directory and any necessary parent directories if they don't exist.
	- ```${dir $@}``` : This is a special function that extracts the directory part of the target file path (```$@```). In this case, it extracts the directory part of ```${OBJS_DIR}/%.o```. So, if ```${OBJS_DIR}``` is ```obj``` and ```$@``` represents ```${OBJS_DIR}/file.o```, ```${dir $@}``` evaluates to ```obj```.
- ```${CC} ${CFLAGS} -c $< -o $@``` : This command compiles the source file (```$<```) into an object file (```$@```). ```${CC}``` is the compiler, ```${CFLAGS}``` are the compiler flags, ```-c``` indicates compilation without linking, and ```-o $@``` specifies the output file.
	- The ```-c``` flag in the compilation command ```${CC} ${CFLAGS} -c $< -o $@``` stands for __"compile only"__ or __"compile source files into object files without linking."__ When you include the ```-c``` flag, the compiler ```(${CC})``` compiles the source file specified by ```$<``` into an object file (specified by ```-o $@```) without performing the linking stage, which combines multiple object files into an executable or a library. If you remove the ```-c``` flag from the compilation command, the command will still work, but it will attempt to perform both compilation and linking in a single step. This might lead to errors because the compiler expects to receive object files as input when performing linking, not source files.
	- The ```-o``` flag in the compilation command ```${CC} ${CFLAGS} -c $< -o $@``` is used to specify _the output file_.

- ```$<``` and ```$@``` are automatic variables in a Makefile, each with a specific meaning :
	- ```$<``` : This represents the first prerequisite of the rule. In the context of compiling a source file into an object file, ```$<``` refers to the source file that needs to be compiled. It's typically used on the command line to indicate the input file for the compilation process.
	- ```$@``` : This represents the target of the rule. In the context of compiling a source file into an object file, ```$@``` refers to the object file that is being generated. It's typically used on the command line to indicate the output file for the compilation process.
	- So, in the command ```${CC} ${CFLAGS} -c $< -o $@```, ```$<``` is replaced by the source file (.c file), and ```$@``` is replaced by the object file (.o file) that is being generated by compiling the source file.

---

    all: ${NAME}

- ```all: ${NAME}``` This is the default target. It depends on the ```${NAME}``` target, which means that running ```make``` without specifying a target will build the ```${NAME}``` target.

---

    ${NAME}: ${OBJS} ${LIBFT_PATH}/${LIBFT_NAME} ${MINILIBX_PATH}/${MINILIBX_NAME}
    ${CC} ${CFLAGS} -o ${NAME} ${OBJS} -L${LIBFT_PATH} -lft -L${MINILIBX_PATH} -lmlx -lm

- ```${NAME}: ${OBJS} ${LIBFT_PATH}/${LIBFT_NAME} ${MINILIBX_PATH}/${MINILIBX_NAME}``` : This target specifies that the ```${NAME}``` executable depends on ```${OBJS}``` (object files), ```${LIBFT_PATH}/${LIBFT_NAME}``` (libft library), and ```${MINILIBX_PATH}/${MINILIBX_NAME}``` (minilibx library).
- ```${CC} ${CFLAGS} -o ${NAME} ${OBJS} -L${LIBFT_PATH} -lft -L${MINILIBX_PATH} -lmlx -lm``` : This command links the object files ```${OBJS}``` with the libft.a and libmlx.a libraries, and creates the ```${NAME}``` executable. ```-L${LIBFT_PATH}``` and ```-L${MINILIBX_PATH}``` specify the directories where the libraries are located, ```-lft``` and ```-lmlx``` specify the libraries to link against, and ```-lm``` links the math library.

---

    ${LIBFT_PATH}/${LIBFT_NAME}:
    make -C ${LIBFT_PATH}
    ${MINILIBX_PATH}/${MINILIBX_NAME}:
    make -C ${MINILIBX_PATH}


- ```${LIBFT_PATH}/${LIBFT_NAME}``` : This rule specifies how to build the libft.a library. It depends on the libft directory and triggers a ```make``` command in that directory.
- ```${MINILIBX_PATH}/${MINILIBX_NAME}``` : This rule specifies how to build the libmlx.a library. It depends on the Minilibx directory and triggers a ```make``` command in that directory.
- The ```-C``` option in the make command stands for "change directory."

---

    clean:
        rm -r ${OBJS_DIR}
        make clean -C ${MINILIBX_PATH}
        make clean -C ${LIBFT_PATH}

- __clean__ : This target removes the ```${OBJS_DIR}``` directory containing object files, and calls make clean in ```${MINILIBX_PATH}``` and ```${LIBFT_PATH}``` to clean object files in those directories.

---

    fclean: clean
        rm -f ${NAME}
        rm -f ${LIBFT_PATH}/${LIBFT_NAME}
        rm -f ${MINILIBX_PATH}/${MINILIBX_NAME}

- __fclean__ : This target depends on the clean target. It removes the ```${NAME}``` executable, libft.a, and libmlx.a library files.

---

    re: fclean all

- __re__ : This target depends on the fclean and all targets. It cleans the project and rebuilds it from scratch.

---

    .PHONY: all re clean fclean

- The __.PHONY__ special target is used to prevent conflicts with files of the same name as the targets. When you declare a target as phony using .PHONY, it tells ```make``` that the target is not an actual file that make should check for changes. Instead, it's just a name for a specific action or task.

---

## COMPREHENSION AND DEFINITIONS

### RULES AND TARGETS

In the context of a Makefile, a __"target"__ refers to something that needs to be built or updated. It can be a file, a set of files, or just a label representing an action or task.

__Targets are typically associated with rules in a Makefile__. Each rule specifies how to build or update a target.

__The rule consists of a _target_, _prerequisites_, and _commands_.__

- __Target__ : This is the item that needs to be built or updated. It can be a file name, indicating that the target is a file that needs to be created or updated, or it can be a label that represents an action or task that needs to be performed.

- __Prerequisites__ : These are the items that the target depends on. They can be other files, directories, or labels representing other targets. If any of the prerequisites are newer than the target, or if they don't exist, the target needs to be rebuilt or updated.

- __Commands__ : These are the shell commands that need to be executed in order to build or update the target. They typically perform tasks such as compiling source code, linking object files, copying files, or running tests.

_Here's an example of a rule in a Makefile:_

```
output.txt: input.txt
    cp input.txt output.txt
```

In this rule:

- output.txt is the target.
- input.txt is the prerequisite.
- cp input.txt output.txt is the command that needs to be executed to update the target (output.txt) using the prerequisite (input.txt).

When you run make, make analyzes the dependencies between targets and their prerequisites, and then executes the commands associated with each rule to update the targets as needed.
