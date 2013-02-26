Reverse enum lookup
###################
:date: 2009-03-06 05:22
:author: Laurence Noton
:category: Development

Provide the capability to reverse lookup and enum when dealing with only
the raw value.

``public enum Type {``

ONE(1), TWO(2), THREE(3);

private int value;

private Type(int value) {

this.value = value;

}

private static final Map lookup = new HashMap();

static {

for (Type s : EnumSet.allOf(Type.class))

lookup.put(s.getValue(), s);

}

public static Type get(int value) {

return lookup.get(value);

}

/\*\*

\* @return the value

\*/

public int getValue() {

return value;

}

}
