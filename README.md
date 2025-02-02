<h1 id="runoff"><a data-id="" href="#runoff">Runoff</a></h1>

<p>Implement a program that runs a runoff election, per the below.</p>

<div class="highlighter-rouge"><div class="highlight"><pre class="highlight"><code>./runoff Alice Bob Charlie
Number of voters: 5
Rank 1: Alice
Rank 2: Bob
Rank 3: Charlie

Rank 1: Alice
Rank 2: Charlie
Rank 3: Bob

Rank 1: Bob
Rank 2: Charlie
Rank 3: Alice

Rank 1: Bob
Rank 2: Alice
Rank 3: Charlie

Rank 1: Charlie
Rank 2: Alice
Rank 3: Bob

Alice
</code></pre></div></div>

<h2 id="background"><a data-id="" href="#background">Background</a></h2>

<p>You already know about plurality elections, which follow a very simple algorithm for determining the winner of an election: every voter gets one vote, and the candidate with the most votes wins.</p>

<p>But the plurality vote does have some disadvantages. What happens, for instance, in an election with three candidates, and there is a tie between two candidates? In a plurality vote where each voter chooses their first preference only, Charlie wins this election with four votes compared to only three for Bob and two for Alice. But a majority of the voters (5 out of the 9) would be happier with either Alice or Bob instead of Charlie. By considering ranked preferences, a voting system may be able to choose a winner that better reflects the preferences of the voters.</p>

<p>One such ranked choice voting system is the instant runoff system. In an instant runoff election, voters can rank as many candidates as they wish. If any candidate has a majority (more than 50%) of the first preference votes, that candidate is declared the winner of the election.</p>

<p>If no candidate has more than 50% of the vote, then an “instant runoff” occurrs. The candidate who received the fewest number of votes is eliminated from the election, and anyone who originally chose that candidate as their first preference now has their second preference considered. Why do it this way? Effectively, this simulates what would have happened if the least popular candidate had not been in the election to begin with.</p>

<p>The process repeats: if no candidate has a majority of the votes, the last place candidate is eliminated, and anyone who voted for them will instead vote for their next preference (who hasn’t themselves already been eliminated). Once a candidate has a majority, that candidate is declared the winner.</p>

<p>Let’s consider the nine ballots above and examine how a runoff election would take place.</p>

<p>Alice has two votes, Bob has three votes, and Charlie has four votes. To win an election with nine people, a majority (five votes) is required. Since nobody has a majority, a runoff needs to be held. Alice has the fewest number of votes (with only two), so Alice is eliminated. The voters who originally voted for Alice listed Bob as second preference, so Bob gets the extra two vote. Bob now has five votes, and Charlie still has four votes. Bob now has a majority, and Bob is declared the winner.</p>

<p>What corner cases do we need to consider here?</p>

<p>One possibility is that there’s a tie for who should get eliminated. We can handle that scenario by saying all candidates who are tied for last place will be eliminated. If every remaining candidate has the exact same number of votes, though, eliminating the tied last place candidates means eliminating everyone! So in that case, we’ll have to be careful not to eliminate everyone, and just declare the election a tie between all remaining candidates.</p>

<p>Some instant runoff elections don’t require voters to rank all of their preferences — so there might be five candidates in an election, but a voter might only choose two. For this problem’s purposes, though, we’ll ignore that particular corner case, and assume that all voters will rank all of the candidates in their preferred order.</p>

<p>Sounds a bit more complicated than a plurality vote, doesn’t it? But it arguably has the benefit of being an election system where the winner of the election more accurately represents the preferences of the voters.</p>


<h2 id="understanding"><a data-id="" href="#understanding">Understanding</a></h2>

<p>Let’s open up <code class="highlighter-rouge">runoff.c</code> to take a look at what’s already there. We’re defining two constants: <code class="highlighter-rouge">MAX_CANDIDATES</code> for the maximum number of candidates in the election, and <code class="highlighter-rouge">MAX_VOTERS</code> for the maximum number of voters in the election.</p>

<p>Next up is a two-dimensional array <code class="highlighter-rouge">preferences</code>. The array <code class="highlighter-rouge">preferences[i]</code> will represent all of the preferences for voter number <code class="highlighter-rouge">i</code>, and the integer <code class="highlighter-rouge">preferences[i][j]</code> here will store the index of the candidate who is the <code class="highlighter-rouge">j</code>th preference for voter <code class="highlighter-rouge">i</code>.</p>

<p>Next up is a <code class="highlighter-rouge">struct</code> called <code class="highlighter-rouge">candidate</code>. Every <code class="highlighter-rouge">candidate</code> has a <code class="highlighter-rouge">string</code> field for their <code class="highlighter-rouge">name</code>, and <code class="highlighter-rouge">int</code> representing the number of <code class="highlighter-rouge">votes</code> they currently have, and a <code class="highlighter-rouge">bool</code> value called <code class="highlighter-rouge">eliminated</code> that indicates whether the candidate has been eliminated from the election. The array <code class="highlighter-rouge">candidates</code> will keep track of all of the candidates in the election.</p>

<p>The program also has two global variables: <code class="highlighter-rouge">voter_count</code> and <code class="highlighter-rouge">candidate_count</code>.</p>

<p>Now onto <code class="highlighter-rouge">main</code>. Notice that after determining the number of candidates and the number of voters, the main voting loop begins, giving every voter a chance to vote. As the voter enters their preferences, the <code class="highlighter-rouge">vote</code> function is called to keep track of all of the preferences. If at any point, the ballot is deemed to be invalid, the program exits.</p>

<p>Once all of the votes are in, another loop begins: this one’s going to keep looping through the runoff process of checking for a winner and eliminating the last place candidate until there is a winner.</p>

<p>The first call here is to a function called <code class="highlighter-rouge">tabulate</code>, which should look at all of the voters’ preferences and compute the current vote totals, by looking at each voter’s top choice candidate who hasn’t yet been eliminated. Next, the <code class="highlighter-rouge">print_winner</code> function should print out the winner if there is one; if there is, the program is over. But otherwise, the program needs to determine the fewest number of votes anyone still in the election received (via a call to <code class="highlighter-rouge">find_min</code>). If it turns out that everyone in the election is tied with the same number of votes (as determined by the <code class="highlighter-rouge">is_tie</code> function), the election is declared a tie; otherwise, the last-place candidate (or candidates) is eliminated from the election via a call to the <code class="highlighter-rouge">eliminate</code> function.</p>

<p>If you look a bit further down in the file, you’ll see that these functions — <code class="highlighter-rouge">vote</code>, <code class="highlighter-rouge">tabulate</code>, <code class="highlighter-rouge">print_winner</code>, <code class="highlighter-rouge">find_min</code>, <code class="highlighter-rouge">is_tie</code>, and <code class="highlighter-rouge">eliminate</code> — are all left up to you to complete!</p>

<h2 id="specification"><a data-id="" href="#specification">Specification</a></h2>

<p>Complete the implementation of <code class="highlighter-rouge">runoff.c</code> in such a way that it simulates a runoff election. You should complete the implementations of the <code class="highlighter-rouge">vote</code>, <code class="highlighter-rouge">tabulate</code>, <code class="highlighter-rouge">print_winner</code>, <code class="highlighter-rouge">find_min</code>, <code class="highlighter-rouge">is_tie</code>, and <code class="highlighter-rouge">eliminate</code> functions, and you should not modify anything else in <code class="highlighter-rouge">runoff.c</code> (except you may include additional header files, if you’d like).</p>

<h3 id="vote"><a data-id="" href="#vote"><code class="highlighter-rouge">vote</code></a></h3>

<p>Complete the <code class="highlighter-rouge">vote</code> function.</p>

<ul class="fa-ul">
  <li data-marker="*"><span class="fa-li"><i class="fas fa-circle"></i></span>The function takes arguments <code class="highlighter-rouge">voter</code>, <code class="highlighter-rouge">rank</code>, and <code class="highlighter-rouge">name</code>. If <code class="highlighter-rouge">name</code> is a match for the name of a valid candidate, then you should update the global preferences array to indicate that the voter <code class="highlighter-rouge">voter</code> has that candidate as their <code class="highlighter-rouge">rank</code> preference (where <code class="highlighter-rouge">0</code> is the first preference, <code class="highlighter-rouge">1</code> is the second preference, etc.).</li>
  <li data-marker="*"><span class="fa-li"><i class="fas fa-circle"></i></span>If the preference is successfully recorded, the function should return <code class="highlighter-rouge">true</code>; the function should return <code class="highlighter-rouge">false</code> otherwise (if, for instance, <code class="highlighter-rouge">name</code> is not the name of one of the candidates).</li>
  <li data-marker="*"><span class="fa-li"><i class="fas fa-circle"></i></span>You may assume that no two candidates will have the same name.</li>
</ul>

<details>
    <summary>Hints</summary>
    <ul class="fa-ul">
  <li data-marker="*"><span class="fa-li"><i class="fas fa-circle"></i></span>Recall that <code class="highlighter-rouge">candidate_count</code> stores the number of candidates in the election.</li>
  <li data-marker="*"><span class="fa-li"><i class="fas fa-circle"></i></span>Recall that you can use <a href="https://man.cs50.io/3/strcmp"><code class="highlighter-rouge">strcmp</code></a> to compare two strings.</li>
  <li data-marker="*"><span class="fa-li"><i class="fas fa-circle"></i></span>Recall that <code class="highlighter-rouge">preferences[i][j]</code> stores the index of the candidate who is the <code class="highlighter-rouge">j</code>th ranked preference for the <code class="highlighter-rouge">i</code>th voter.</li>
</ul>
</details>

<h3 id="tabulate"><a data-id="" href="#tabulate"><code class="highlighter-rouge">tabulate</code></a></h3>

<p>Complete the <code class="highlighter-rouge">tabulate</code> function.</p>

<ul class="fa-ul">
  <li data-marker="*"><span class="fa-li"><i class="fas fa-circle"></i></span>The function should update the number of <code class="highlighter-rouge">votes</code> each candidate has at this stage in the runoff.</li>
  <li data-marker="*"><span class="fa-li"><i class="fas fa-circle"></i></span>Recall that at each stage in the runoff, every voter effectively votes for their top-preferred candidate who has not already been eliminated.</li>
</ul>

<details>
    <summary>Hints</summary>
    <ul class="fa-ul">
  <li data-marker="*"><span class="fa-li"><i class="fas fa-circle"></i></span>Recall that <code class="highlighter-rouge">voter_count</code> stores the number of voters in the election.</li>
  <li data-marker="*"><span class="fa-li"><i class="fas fa-circle"></i></span>Recall that for a voter <code class="highlighter-rouge">i</code>, their top choice candidate is represented by <code class="highlighter-rouge">preferences[i][0]</code>, their second choice candidate by <code class="highlighter-rouge">preferences[i][1]</code>, etc.</li>
  <li data-marker="*"><span class="fa-li"><i class="fas fa-circle"></i></span>Recall that the <code class="highlighter-rouge">candidate</code> <code class="highlighter-rouge">struct</code> has a field called <code class="highlighter-rouge">eliminated</code>, which will be <code class="highlighter-rouge">true</code> if the candidate has been eliminated from the election.</li>
  <li data-marker="*"><span class="fa-li"><i class="fas fa-circle"></i></span>Recall that the <code class="highlighter-rouge">candidate</code> <code class="highlighter-rouge">struct</code> has a field called <code class="highlighter-rouge">votes</code>, which you’ll likely want to update for each voter’s preferred candidate.</li>
</ul>
</details>

<h3 id="print_winner"><a data-id="" href="#print_winner"><code class="highlighter-rouge">print_winner</code></a></h3>

<p>Complete the <code class="highlighter-rouge">print_winner</code> function.</p>

<ul class="fa-ul">
  <li data-marker="*"><span class="fa-li"><i class="fas fa-circle"></i></span>If any candidate has more than half of the vote, their name should be printed to <code class="highlighter-rouge">stdout</code> and the function should return <code class="highlighter-rouge">true</code>.</li>
  <li data-marker="*"><span class="fa-li"><i class="fas fa-circle"></i></span>If nobody has won the election yet, the function should return <code class="highlighter-rouge">false</code>.</li>
</ul>

<details>
    <summary>Hints</summary>
    <ul class="fa-ul">
  <li data-marker="*"><span class="fa-li"><i class="fas fa-circle"></i></span>Recall that <code class="highlighter-rouge">voter_count</code> stores the number of voters in the election. Given that, how would you express the number of votes needed to win the election?</li>
</ul>
</details>

<h3 id="find_min"><a data-id="" href="#find_min"><code class="highlighter-rouge">find_min</code></a></h3>

<p>Complete the <code class="highlighter-rouge">find_min</code> function.</p>

<ul class="fa-ul">
  <li data-marker="*"><span class="fa-li"><i class="fas fa-circle"></i></span>The function should return the minimum vote total for any candidate who is still in the election.</li>
</ul>

<details>
    <summary>Hints</summary>
    <ul class="fa-ul">
  <li data-marker="*"><span class="fa-li"><i class="fas fa-circle"></i></span>You’ll likely want to loop through the candidates to find the one who is both still in the election and has the fewest number of votes. What information should you keep track of as you loop through the candidates?</li>
</ul>
</details>

<h3 id="is_tie"><a data-id="" href="#is_tie"><code class="highlighter-rouge">is_tie</code></a></h3>

<p>Complete the <code class="highlighter-rouge">is_tie</code> function.</p>

<ul class="fa-ul">
  <li data-marker="*"><span class="fa-li"><i class="fas fa-circle"></i></span>The function takes an argument <code class="highlighter-rouge">min</code>, which will be the minimum number of votes that anyone in the election currently has.</li>
  <li data-marker="*"><span class="fa-li"><i class="fas fa-circle"></i></span>The function should return <code class="highlighter-rouge">true</code> if every candidate remaining in the election has the same number of votes, and should return <code class="highlighter-rouge">false</code> otherwise.</li>
</ul>

<details>
    <summary>Hints</summary>
    <ul class="fa-ul">
  <li data-marker="*"><span class="fa-li"><i class="fas fa-circle"></i></span>Recall that a tie happens if every candidate still in the election has the same number of votes. Note, too, that the <code class="highlighter-rouge">is_tie</code> function takes an argument <code class="highlighter-rouge">min</code>, which is the smallest number of votes any candidate currently has. How might you use that information to determine if the election is a tie (or, conversely, not a tie)?</li>
</ul>
</details>

<h3 id="eliminate"><a data-id="" href="#eliminate"><code class="highlighter-rouge">eliminate</code></a></h3>

<p>Complete the <code class="highlighter-rouge">eliminate</code> function.</p>

<ul class="fa-ul">
  <li data-marker="*"><span class="fa-li"><i class="fas fa-circle"></i></span>The function takes an argument <code class="highlighter-rouge">min</code>, which will be the minimum number of votes that anyone in the election currently has.</li>
  <li data-marker="*"><span class="fa-li"><i class="fas fa-circle"></i></span>The function should eliminate the candidate (or candidates) who have <code class="highlighter-rouge">min</code> number of votes.</li>
</ul>



