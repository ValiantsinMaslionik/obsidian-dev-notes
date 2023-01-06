#aspnet_core/Routing

---

![[zIMG-ASP.NET-roting-segments.png]]

URL Path|Description
--|--
/capital|No match—too few segments
/capital/europe/uk|No match—too many segments
/name/uk|No match—first segment is not capital
/capital/uk|Matches
