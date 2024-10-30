"""
Driver: Krista Mathew
Navigator: Katherine Fuentes, Jigyasa Dahal, Afreen Ahmed
Assignment: Exercise 8 
Date: 10/29/24 
"""

from argparse import ArgumentParser
import sys

def get_winner(electors, hypo):
    """Calculates the number of electoral votes received by each candidate
    
    Args:
    electors (dict): contains integer values for the number of electors by state
    hypo (dict): contains either "R" or "D" values coressponding to the hypothetical winning candidate's party
    
    Returns:
    tuple: a tuple of the winner based on party ("R" or "D") and the number of votes as a total integer
    """
    r_votes = 0
    d_votes = 0
    
    for state, party in hypo.items(): #running count of the electoral votes for each party
        votes = electors.get(state, 0) #sets the state from electors and sets votes to zero
        if party == "R":
            r_votes += votes #adds votes to R if party is R
        elif party == "D":
            d_votes += votes #adds votes to D if party is D
    
    if r_votes > d_votes: #returns winning party and number of votes
        return "R", r_votes
    else:
        return "D", d_votes
        
def to_dict(filename, value_type=str):
    """ Read a two-column, tab-delimited file and return the lines as
    a dictionary with data from the first column as keys and data from
    the second column as values.
    Args:
    filename (str): the file to be read.
    value_type (data type): the type of the values in the second
    column (default: str).
    Raises:
    ValueError: encountered a line with the wrong number of columns.
    Returns:
    dict: the data from the file.
    """
    lines = dict()
    with open(filename, "r", encoding="utf-8") as f:
        for n, line in enumerate(f, start=1):
            line = line.strip()
            if not line:
                continue
        values = line.split("\t")
        if len(values) != 2:
            raise ValueError("unexpected number of columns on line"
                            f" {n} of {filename}")
        lines[values[0]] = value_type(values[1])
    return lines

def read_data(elector_file, outcome_file):
    """ Read elector data and outcome data and return as dictionaries.
    Args:
    elector_file (str): path to the file of electors by state.
    outcome_file (str): path to the file of hypothetical outcomes by
    state.
    Returns:
    uple of dict, dict: the elector data and outcome data. Keys in
    each dictionary will be state names.
    """
    elector_dict = to_dict(elector_file, value_type=int)
    outcome_dict = to_dict(outcome_file)
    return elector_dict, outcome_dict

def parse_args(arglist):
    """ Parse command-line arguments.
    Args:
    arglist (list of str): list of arguments from the command line.
    Returns:
    namespace: the parsed arguments, as returned by
    argparse.ArgumentParser.parse_args().
    """
    parser = ArgumentParser()
    parser.add_argument("elector_file", help="file of electors by state")
    parser.add_argument("outcome_file", help="file of outcomes by state")
    return parser.parse_args(arglist)

if __name__ == "__main__":
    args = parse_args(sys.argv[1:])
    elector_dict, outcome_dict = read_data(args.elector_file, args.outcome_file)
    candidate, votes = get_winner(elector_dict, outcome_dict)
    print(f"{candidate} would win with {votes} electoral votes.")
