#net/regex

---

## Implementing custom match evaluator

```cs
public static string RemoveMultipleSemicolonInStyleAttribute(string input)
{
	if (string.IsNullOrWhiteSpace(input))
	{
		return input;
	}
	string MatchEvaluator(Match match) => 
		match.Groups[1].Value + MultipleSemicolonRegex.Replace(match.Groups[2].Value, ";") + match.Groups[3].Value;

	input = MatchToTagDoubleQuotes.Replace(input, MatchEvaluator);
	input = MatchToTagSingleQuotes.Replace(input, MatchEvaluator);
	
	return input;
}
```        