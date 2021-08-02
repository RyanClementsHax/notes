# Flags

- you can implement a flag enum by using the `System.FlagsAttribute` on an enum, where every member is an exponent of 2 starting with 0 (so that they compile down to a single bit in the underlying integer representation)

    ```cs
    [Flags]
    public enum Permissions
    {
        None = 0,
        Account = 1,
        Builds = 2,
        Characters = 4,
        Guilds = 8,
        Inventories = 16,
        Progression = 32,
        Pvp = 64,
        Tradingpost = 128,
        Unlocks = 256,
        Wallet = 512
    }
    ```

- you can represent an `All` member by setting its number to twice the biggest number minus one (assuming that all bits are represented by a member)

    ```cs
    public enum Permissions
    {
        // other members
        All = 1023
    }
    ```

- you can make extension methods to provide methods around this
  - I have found the following to be helpful to expand and combine a flag

    ```cs
    public static class EnumFlagExt
    {
        // breaks a flagged enum into a list of the flags that make it up
        public static IList<T> ToList<T>(this T flags) where T : struct, IConvertible
        {
            var flagsAsInt = (int)(object)flags;
            var list = new List<T>();

            foreach (T flag in Enum.GetValues(typeof(T)))
            {
                if ((flagsAsInt & (int)(object)flag) != 0) list.Add(flag);
            }

            return list;
        }

        // combines a list of flags into one flag representation
        public static T Combine<T>(this IEnumerable<T> permissions) where T : struct, IConvertible
            => (T)(object)permissions.Aggregate(0, (acc, curr) => (int)(object)acc | (int)(object)curr);
    }
    ```
