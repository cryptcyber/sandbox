

You can use the option -C (or --directory if you prefer long options) to give the target directory of your choice in case you are using the Gnu version of tar. The directory should exist:

mkdir foo
tar -xzf bar.tar.gz -C foo

If you are not using a tar capable of extracting to a specific directory, you can simply cd into your target directory prior to calling tar; then you will have to give a complete path to your archive, of course. You can do this in a scoping subshell to avoid influencing the surrounding script:

mkdir foo
(cd foo; tar -xzf ../bar.tar.gz)  # instead of ../ you can use an absolute path as well

Or, if neither an absolute path nor a relative path to the archive file is suitable, you also can use this to name the archive outside of the scoping subshell:

TARGET_PATH=a/very/complex/path/which/might/even/be/absolute
mkdir -p "$TARGET_PATH"
(cd "$TARGET_PATH"; tar -xzf -) < bar.tar.gz


