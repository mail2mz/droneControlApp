# droneControlApp
// Ancillary function for powerset.  It adds a character onto the beginning
// of each string in a vector.
vector<string> addChar (const vector<string>& v, char c) {
    vector<string> ans;
    for (int i=0; i<(int)v.size() ; i++) {
	ans.push_back(c + v.at(i));
    }
    return ans;
}

// Note that I wrote this in scheme first!  Programming "shell game"
// recursion puzzles is easier with weak typing.
// This takes a string and returns the power(multi)set of its characters
// as a vector.  For example, powerset of "aab" would be the vector with
// elements (in no particular order): "aab" "ab" "aa" "a" "ab" "a" "b".
// Assume we don't care about eliminating duplicates for now.
vector<string> powerset (string s) {
    vector<string> ans;
    if (s.size() == 0) {
	ans.push_back("");
    } else {
	char c = s.at(0);
	string rest = s.substr(1);
	vector<string> psetRest = powerset (rest);
	ans = addChar (psetRest, c);
	ans.insert(ans.end(), psetRest.begin(), psetRest.end());
    }
    return ans;
}

//CREATE FUNCTION WHICH RETURNS A STRING THAT EXISTS IN THE TABLE
string bestPermutation (string s, const SmartHashTable& t) {
    //note that all permutations have the same score
    //we just need to check a permutation of the string exists in the table
    //otherwise we will return an empty string
    sort(s.begin(), s.end());
    do {
        if (t.lookup(s)) {
            return s;
        }
    } while (next_permutation (s.begin(), s.end()));
    return "";
}

int main (int argc, char* argv[]) {
    //SETTING UP THE HASHTABLE
    if (argc < 2) {//no command was provided
        cerr << "Error, no word list file name provided." << endl;
        exit(1);
    }
    string wordlistFileName = argv[1];
    string token;
    ifstream myInputFile (wordlistFileName.c_str());
    if (!myInputFile) {//file does not exist
        cerr << "Error, couldn't open word list file." << endl;
        exit(1);
    }
    SmartHashTable t(1000000);
    myInputFile >> token;
    while (myInputFile) {
        t.insert(token);
        myInputFile >> token;
    }
    myInputFile.close();

    //FINDING SCORES
    string word = "";
    int score = 0;
    cin >> token;
    while (cin) {
        //process word
        vector<string> permWord = powerset(token);
        for (int i = 0; i < permWord.size(); i++) {
            permWord[i]= bestPermutation(permWord[i], t);//store a word that exists in the spot, instead of the permutation
            if (permWord[i] != "" && scrabbleValue(permWord[i]) > score) {
                //a word exists and has a larger score than anything before it
                score = scrabbleValue(permWord[i]);
                word = permWord[i];
            }
        }
        if (score == 0) {//no word exists
            cout << token << ": no matches" << endl;
        } else {
            cout << token << ": " << word << " has score of " << score << endl;
        }
        //go to next word
        word = "";
        score = 0;
        cin >> token;
    }

}
