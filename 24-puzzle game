#!/bin/bash

declare -A matrix # This is a 2D array for initial state of grid
declare -A goalState # This is a 2D array for goal state of grid
declare -a generated_numbers # This is 1D array to store generated random numbers
declare -a legal_moves # This is a 1D array to store the legal moves a user can perform on a current state of the grid.
declare -a path # This is a 1D array used to track user's operations (Up, Down, Right, Left)
declare current_Row # This variable is used to store the row number of blank index
declare current_Col # This variable is used to store the column number of blank index.


#This function generates a 1D array of random numbers
generate_random_numbers () {
	unset generated_numbers #resetting the generated number array since we donot need to previous value of array.
	while [ ${#generated_numbers[@]} -lt 25 ]; do
        random_number=$((1 + $RANDOM % 25))
        if [[ ! " ${generated_numbers[@]} " =~ " $random_number " ]]; then # Checking if the generated number is already in the array or not as the values should not repeat
            generated_numbers+=($random_number)
        fi
    done
}

#This function prints the initial state and final state.
print_matrix () {
	echo "---- Current State ----"

    for ((i = 0; i < 5; i++)); do
        for ((j = 0; j < 5; j++)); do
        	if [ ${matrix[$i,$j]} -ge 10 ]; then
	        	echo -n "${matrix[$i,$j]} | "
	        elif [ ${matrix[$i,$j]} -eq -1 ]; then
	        	echo -n "   | "
        	else
		    	echo -n "${matrix[$i,$j]}  | "    	
        	fi
        done
        echo
    done

    echo -e "\n\n\n------ Goal State ------"

    for ((i = 0; i < 5; i++)); do
        for ((j = 0; j < 5; j++)); do
            if [ ${goalState[$i,$j]} -ge 10 ]; then
	        	echo -n "${goalState[$i,$j]} | "
	        elif [ ${goalState[$i,$j]} -eq -1 ]; then
	        	echo -n "   | "
        	else
		    	echo -n "${goalState[$i,$j]}  | "    	
        	fi
        done
        echo
    done
}

#This function stores of values of 1D into a grid of 5x5.
init() {
	generate_random_numbers
    count=0    #To keep track of index of generated array.

    for ((i = 0; i < 5; i++)); do
        for ((j = 0; j < 5; j++)); do
        	if [ ${generated_numbers[count]} -eq 25 ]; then
        		matrix[$i,$j]=-1	
        		current_Row=$i
        		current_Col=$j
        	else
            	matrix[$i,$j]=${generated_numbers[count]}
            fi
            count=$((count + 1))   # Allows to generate elements from generated array to the cell of the matrix,except for the value 25 which is treated as null space.
        done
    done
}

#This function store values of 1D array into a 5x5 Goal State grid.
goal () {
	while true; do
		generate_random_numbers

		count=0

		for ((i = 0; i < 5; i++)); do
		    for ((j = 0; j < 5; j++)); do
		    	if [ ${generated_numbers[count]} -eq 25 ]; then
		    		goalState[$i,$j]=-1	
		    	else
		        	goalState[$i,$j]=${generated_numbers[count]}
		        fi
		        count=$((count + 1))
		    done
		done            #To ensure that the game can be won.
		Is_Solveable    #checks that game must not run in unsolveable state.
		if [ $? -eq 0 ]; then   #If unsolveable then loop continous and generating new set of random numbers for the goal state.
			break
		fi
	done
}

#This function checks if user has reached goal state or not
is_goal () {
	for ((i = 0; i < 5; i++)); do
        for ((j = 0; j < 5; j++)); do
        	if [ ${goalState[$i,$j]} -ne ${matrix[$i,$j]} ]; then
        		return 0
        	fi
        done
    done
    return 1
}

calculate_inversion_count() {
	local inversion_count=0

    for i in {1..24}; do
        local i_row
        local i_col

        # Find the row and column of the current number 'i' in the initial state
        for ((row = 0; row < 5; row++)); do
            for ((col = 0; col < 5; col++)); do
                if [ "${matrix[$row,$col]}" == "$i" ]; then
                    i_row=$row
                    i_col=$col
                    break 2  # Exit both loops
                fi
            done
        done

        for j in {1..24}; do
            local j_row
            local j_col

            # Find the row and column of the number 'j' following 'i' in the initial state
            for ((row = 0; row < 5; row++)); do
                for ((col = 0; col < 5; col++)); do
                    if [ "${matrix[$row,$col]}" == "$j" ]; then
                        j_row=$row
                        j_col=$col
                        break 2  # Exit both loops
                    fi
                done
            done

            if [ "$i" -lt "$j" ] && ((i_row * 4 + i_col > j_row * 4 + j_col)); then
                ((inversion_count++))
            fi
        done
    done

    echo "$inversion_count"
}

# The function iterates through all pairs of numbers in the puzzle.
# For each pair (i, j), it checks if i appears before j but has a higher number.
# If the condition is met, it increments the inversion count. 
Is_Solveable () {
	local inversion_count
    local blank_row

    inversion_count=$(calculate_inversion_count)
    blank_row=$((current_Row + 1))

    if ((inversion_count % 2 == 0)) && ((blank_row % 2 == 1)); then # If inversion count is an even number and row of blank index is an odd number then goal state is reachable.
        return 1
    fi
    return 0
}

# This function checks the legal moves that a user can perform after every input from user.
# This function is called after user performs operations (Up, Down, Left, Right)
legal_move () {
	unset legal_moves
	
	if [ "$current_Row" -ne 0 ]; then
    	legal_moves+=('U')
	fi

	if [ "$current_Row" -ne 4 ]; then
		legal_moves+=('D')
	fi

	if [ "$current_Col" -ne 0 ]; then
		legal_moves+=('L')
	fi

	if [ "$current_Col" -ne 4 ]; then
		legal_moves+=('R')
	fi
}

# This function prints the path that a user took to reach goal state.
# This function is called after user performs operations (Up, Down, Left, Right)
print_path () {
	echo -n "Path: "
    path_string=""
    for direction in "${path[@]}"; do
        path_string+="$direction->"
    done
    # Remove the trailing "->"
    path_string="${path_string%->}"
    echo "$path_string"
}

# This function is used to perform user operations on grid.
# It is also changing the current row and column of blank index in variables defined above.
make_move () {
	if [[ "$1" == $'\e[A' ]]; then
		path+=('U')
		current_Row=$((current_Row - 1))
		local temp=${matrix[$current_Row,$current_Col]}
    	matrix[$current_Row,$current_Col]=${matrix[$((current_Row + 1)),$current_Col]}
	    matrix[$((current_Row + 1)),$current_Col]=$temp
	elif [[ "$1" == $'\e[B' ]]; then
		path+=('D')
		current_Row=$((current_Row + 1))
		local temp=${matrix[$current_Row,$current_Col]}
    	matrix[$current_Row,$current_Col]=${matrix[$((current_Row - 1)),$current_Col]}
	    matrix[$((current_Row - 1)),$current_Col]=$temp
	elif [[ "$1" == $'\e[C' ]]; then
		path+=('R')
		current_Col=$((current_Col + 1))
		local temp=${matrix[$current_Row,$current_Col]}
    	matrix[$current_Row,$current_Col]=${matrix[$current_Row,$((current_Col - 1))]}
	    matrix[$current_Row,$((current_Col - 1))]=$temp
	elif [[ "$1" == $'\e[D' ]]; then
		path+=('L')
		current_Col=$((current_Col - 1))
		local temp=${matrix[$current_Row,$current_Col]}
    	matrix[$current_Row,$current_Col]=${matrix[$current_Row,$((current_Col + 1))]}
	    matrix[$current_Row,$((current_Col + 1))]=$temp
	fi
}

main () {
	init
	goal
	clear
	
	while true; do
		print_matrix
		echo -e "\n-------Legal Moves-------"
		legal_move
		for move in "${legal_moves[@]}"; do
			if [ "$move" == 'U' ]; then
				echo "'Up Arrow' for move up"
			elif [ "$move" == 'D' ]; then
				echo "'Down Arrow' for move down"
			elif [ "$move" == 'L' ]; then
				echo "'Left Arrow' for move left"
			elif [ "$move" == 'R' ]; then
				echo "'Right Arrow' for move right"
			fi
		done
		read -s -n 3 user_move
		clear
		local check_input=0
		for move in "${legal_moves[@]}"; do
			if [ "$move" == "U" ] && [ "$user_move" == $'\e[A' ]; then
				check_input=1
			elif [ "$move" == 'D' ] && [ "$user_move" == $'\e[B' ]; then
				check_input=1
			elif [ "$move" == 'L' ] && [ "$user_move" == $'\e[D' ]; then
				check_input=1
			elif [ "$move" == 'R' ] && [ "$user_move" == $'\e[C' ]; then
				check_input=1
			fi
		done
		if [ "$check_input" == 0 ]; then
			echo "ERROR: Invalid Move."
		else
			make_move "$user_move"
			print_path
			is_goal
			if [ $? -eq 1 ]; then
				break
			fi
		fi
	done 
}

main
