Ref:- https://www.interviewcake.com/question/java/find-duplicate-files?utm_source=weekly_email&utm_source=drip&utm_campaign=weekly_email&utm_campaign=Interview%20Cake%20Weekly%20Problem%20%23222:%20Does%20This%20Linked%20List%20Have%20A%20Cycle%3F&utm_medium=email&utm_medium=email

You left your computer unlocked and your friend decided to troll you by copying a lot of your files to random spots all over your file system.

Even worse, she saved the duplicate files with random, embarrassing names ("this_is_like_a_digital_wedgie.txt" was clever, I'll give her that).

Write a method that returns a list of all the duplicate files. We'll check them by hand before actually deleting them, since programmatically deleting files is really scary. To help us confirm that two files are actually duplicates, return a list of FilePaths objects with variables for the original and duplicate paths:

  import java.nio.file.Path;

public class FilePaths {

    private Path duplicatePath;
    private Path originalPath;

    public FilePaths(Path duplicatePath, Path originalPath) {
        this.duplicatePath = duplicatePath;
        this.originalPath  = originalPath;
    }

    public Path getDuplicatePath() {
        return duplicatePath;
    }

    public Path getOriginalPath() {
        return originalPath;
    }

    @Override
    public String toString() {
        return String.format("(duplicate: %s, original: %s)", duplicatePath, originalPath);
    }
}
Java
For example:

  [(duplicate: /tmp/parker_is_dumb.mpg, original: /home/parker/secret_puppy_dance.mpg),
(duplicate: /home/trololol.mov, original: /etc/apache2/httpd.conf)]
You can assume each file was only duplicated once.

Gotchas
Are you correctly handling child folders as well as sibling folders? Be careful that you're traversing your file tree correctly...

When you find two files that are the same, don't just choose a random one to mark as the "duplicate." Try to figure out which one your friend made!

Does your solution work correctly if it's an empty file system (meaning the root directory is empty)?

Our solution takes O(n)O(n) time and space, where nn is the number of files. Is your solution order of the total size on disc of all the files? If so, you can do better!

To get our time and space costs down, we took a small hit on accuracy—we might get a small number of false positives. We're okay with that since we'll double-check before actually deleting files.

Breakdown
No idea where to start? Try writing something that just walks through a file system and prints all the file names. If you're not sure how to do that, look it up! Or just make it up. Remember, even if you can’t implement working code, your interviewer will still want to see you think through the problem.

One brute force ↴ solution is to loop over all files in the file system, and for each file look at every other file to see if it's a duplicate. This means n^2n
​2
​​  file comparisons, where nn is the number of files. That seems like a lot.

Let's try to save some time. Can we do this in one walk through our file system?

Instead of holding onto one file and looking for files that are the same, we can just keep track of all the files we've seen so far. What data structure could help us with that?

We'll use a hash map. ↴ When we see a new file, we first check to see if it's in our hash map. If it's not, we add it. If it is, we have a duplicate!

Once we have two duplicate files, how do we know which one is the original? It's hard to be sure, but try to come up with a reasonable heuristic that will probably work most of the time.

Most file systems store the time a file was last edited as metadata on each file. The more recently edited file will probably be the duplicate!

One exception here: lots of processes like to regularly save their state to a file on disc, so that if your computer suddenly crashes the processes can pick up more or less where they left off (this is how Word is able to say "looks like you had unsaved changes last time, want to restore them?"). If your friend duplicated some of those files, the most-recently-edited one may not be the duplicate. But at the risk of breaking our system (we'll make a backup first, obviously.) we'll run with this "most-recently-edited copy of a file is probably the copy our friend made" heuristic.

So our method will walk through the file system, store files in a hash map, and identify the more recently edited file as the copied one when it finds a duplicate. Can you implement this in code?

Here's a start. We'll initialize:

a hash map to hold the files we've already seen
a stack ↴ to hold directories and files as we go through them
a list to hold our output FilePaths objects
  import java.io.File;

import java.nio.file.Files;
import java.nio.file.Path;

import java.util.ArrayDeque;
import java.util.ArrayList;
import java.util.Deque;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

public static class FilePaths {

    private Path duplicatePath;
    private Path originalPath;

    public FilePaths(Path duplicatePath, Path originalPath) {
        this.duplicatePath = duplicatePath;
        this.originalPath  = originalPath;
    }

    public Path getDuplicatePath() {
        return duplicatePath;
    }

    public Path getOriginalPath() {
        return originalPath;
    }

    @Override
    public String toString() {
        return String.format("(duplicate: %s, original: %s)", duplicatePath, originalPath);
    }
}

public static List<FilePaths> findDuplicateFiles(Path startingDirectory) {

    Map<String, FileInfo> filesSeenAlready = new HashMap<>();
    Deque<Path> stack = new ArrayDeque<>();
    stack.push(startingDirectory);

    List<FilePaths> duplicates = new ArrayList<>();

    while (!stack.isEmpty()) {

        Path currentPath = stack.pop();

    }

    return duplicates;
}

(We're going to make our method iterative instead of recursive to avoid stack overflow. ↴ )

Here's one solution:

  import java.io.File;
import java.io.IOException;

import java.nio.file.Files;
import java.nio.file.Path;

import java.util.ArrayDeque;
import java.util.ArrayList;
import java.util.Deque;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

public static class FilePaths {

    private Path duplicatePath;
    private Path originalPath;

    public FilePaths(Path duplicatePath, Path originalPath) {
        this.duplicatePath = duplicatePath;
        this.originalPath  = originalPath;
    }

    public Path getDuplicatePath() {
        return duplicatePath;
    }

    public Path getOriginalPath() {
        return originalPath;
    }

    @Override
    public String toString() {
        return String.format("(duplicate: %s, original: %s)", duplicatePath, originalPath);
    }
}


private static class FileInfo {

    long timeLastEdited;
    Path path;

    FileInfo(long timeLastEdited, Path path) {
        this.timeLastEdited = timeLastEdited;
        this.path = path;
    }
}

public List<FilePaths> findDuplicateFiles(Path startingDirectory) {
    Map<String, FileInfo> filesSeenAlready = new HashMap<>();
    Deque<Path> stack = new ArrayDeque<>();
    stack.push(startingDirectory);

    List<FilePaths> duplicates = new ArrayList<>();

    while (!stack.isEmpty()) {

        Path currentPath = stack.pop();
        File currentFile = currentPath.toFile();

        // if it's a directory,
        // put the contents in our stack
        if (currentFile.isDirectory()) {
            for (File file : currentFile.listFiles()) {
                stack.push(file.toPath());
            }

        // if it's a file
        } else {

            // get its contents
            String fileContents = null;
            try {
                byte[] encodedFile = Files.readAllBytes(currentPath);
                fileContents = new String(encodedFile, "UTF-8");
            } catch (IOException e) {

                // show error and skip this file
                System.out.println(e);
                continue;
            }

            // get its last edited time
            long currentLastEditedTime = currentFile.lastModified();

            // if we've seen it before
            if (filesSeenAlready.containsKey(fileContents)) {

                FileInfo existingFileInfo = filesSeenAlready.get(fileContents);

                if (currentLastEditedTime > existingFileInfo.timeLastEdited) {

                    // current file is the dupe!
                    duplicates.add(new FilePaths(currentPath, existingFileInfo.path));

                } else {

                    // old file is the dupe!
                    duplicates.add(new FilePaths(existingFileInfo.path, currentPath));

                    // but also update filesSeenAlready to have the new file's info
                    filesSeenAlready.put(fileContents, new FileInfo(currentLastEditedTime, currentPath));
                }

            // if it's a new file, throw it in filesSeenAlready
            // and record its path and last edited time,
            // so we can tell later if it's a dupe
            } else {
                filesSeenAlready.put(fileContents, new FileInfo(currentLastEditedTime, currentPath));
            }
        }
    }

    return duplicates;
}

Okay, this'll work! What are our time and space costs?

We're putting the full contents of every file in our hash map! This costs O(b)O(b) time and space, where bb is the total amount of space taken up by all the files on the file system.

That space cost is pretty unwieldy—we need to store a duplicate copy of our entire filesystem (like, several gigabytes of cat videos alone) in working memory!

Can we trim that space cost down? What if we're okay with losing a bit of accuracy (as in, we do a more "fuzzy" match to see if two files are the same)?

What if instead of making our hash map keys the entire file contents, we hashed ↴ those contents first? So we'd store a constant-size "fingerprint" of the file in our hash map, instead of the whole file itself. This would give us O(1)O(1) space per file (O(n)O(n) space overall, where nn is the number of files)!

That's a huge improvement. But we can take this a step further! While we're making the file matching "fuzzy," can we use a similar idea to save some time? Notice that our time cost is still order of the total size of our files on disc, while our space cost is order of the number of files.

For each file, we have to look at every bit that the file occupies in order to hash it and take a "fingerprint." That's why our time cost is high. Can we fingerprint a file in constant time instead?

What if instead of hashing the whole contents of each file, we hashed three fixed-size "samples" from each file made of the first xx bytes, the middle xx bytes, and the last xx bytes? This would let us fingerprint a file in constant time!

How big should we make our samples?

When your disc does a read, it grabs contents in constant-size chunks, called "blocks."

How big are the blocks? It depends on the file system. My super-hip Macintosh uses a file system called HFS+, which has a default block size of 4Kb (4,000 bytes) per block.

So we could use just 100 bytes each from the beginning middle and end of our files, but each time we grabbed those bytes, our disc would actually be grabbing 4000 bytes, not just 100 bytes. We'd just be throwing the rest away. We might as well use all of them, since having a bigger picture of the file helps us ensure that the fingerprints are unique. So our samples should be the the size of our file system's block size.

Solution
We walk through our whole file system iteratively. As we go, we take a "fingerprint" of each file in constant time by hashing the first few, middle few, and last few bytes. We store each file's fingerprint in a hash map as we go.

If a given file's fingerprint is already in our hash map, we assume we have a duplicate. In that case, we assume the file edited most recently is the one created by our friend.

  import java.io.File;
import java.io.IOException;
import java.io.InputStream;
import java.io.FileInputStream;

import java.math.BigInteger;

import java.nio.file.Path;

import java.security.DigestInputStream;
import java.security.NoSuchAlgorithmException;
import java.security.MessageDigest;

import java.util.ArrayDeque;
import java.util.ArrayList;
import java.util.Deque;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

public static class FilePaths {

    private Path duplicatePath;
    private Path originalPath;

    public FilePaths(Path duplicatePath, Path originalPath) {
        this.duplicatePath = duplicatePath;
        this.originalPath  = originalPath;
    }

    public Path getDuplicatePath() {
        return duplicatePath;
    }

    public Path getOriginalPath() {
        return originalPath;
    }

    @Override
    public String toString() {
        return String.format("(duplicate: %s, original: %s)", duplicatePath, originalPath);
    }
}


private static class FileInfo {

    long timeLastEdited;
    Path path;

    FileInfo(long timeLastEdited, Path path) {
        this.timeLastEdited = timeLastEdited;
        this.path = path;
    }
}


public static List<FilePaths> findDuplicateFiles(Path startingDirectory) {
    Map<String, FileInfo> filesSeenAlready = new HashMap<>();
    Deque<Path> stack = new ArrayDeque<>();
    stack.push(startingDirectory);

    List<FilePaths> duplicates = new ArrayList<>();

    while (!stack.isEmpty()) {

        Path currentPath = stack.pop();
        File currentFile = new File(currentPath.toString());

        // if it's a directory,
        // put the contents in our stack
        if (currentFile.isDirectory()) {
            for (File file : currentFile.listFiles()) {
                stack.push(file.toPath());
            }

        // if it's a file
        } else {

            // get its hash
            String fileHash;
            try {
                fileHash = sampleHashFile(currentPath);
            } catch (IOException | NoSuchAlgorithmException e) {

                // show error and skip this file
                e.printStackTrace();
                continue;
            }

            // get its last edited time
            long currentLastEditedTime = currentFile.lastModified();

            // if we've seen it before
            if (filesSeenAlready.containsKey(fileHash)) {

                FileInfo fileInfo = filesSeenAlready.get(fileHash);
                long existingLastEditedTime = fileInfo.timeLastEdited;
                Path existingPath = fileInfo.path;

                if (currentLastEditedTime > existingLastEditedTime) {

                    // current file is the dupe!
                    duplicates.add(new FilePaths(currentPath, existingPath));

                } else {

                    // old file is the dupe!
                    duplicates.add(new FilePaths(existingPath, currentPath));

                    // but also update filesSeenAlready to have the new file's info
                    filesSeenAlready.put(fileHash, new FileInfo(currentLastEditedTime, currentPath));
                }

            // if it's a new file, throw it in filesSeenAlready
            // and record its path and last edited time,
            // so we can tell later if it's a dupe
            } else {
                filesSeenAlready.put(fileHash, new FileInfo(currentLastEditedTime, currentPath));
            }
        }
    }
    return duplicates;
}

private static final int SAMPLE_SIZE = 4000;

private static String sampleHashFile(Path path) throws IOException, NoSuchAlgorithmException {

    final long totalBytes = new File(path.toString()).length();

    try (InputStream inputStream = new FileInputStream(path.toString())) {
        MessageDigest digest = MessageDigest.getInstance("SHA-512");
        DigestInputStream digestInputStream = new DigestInputStream(inputStream, digest);

        // if the file is too short to take 3 samples, hash the entire file
        if (totalBytes < SAMPLE_SIZE * 3) {
            byte[] bytes = new byte[(int) totalBytes];
            digestInputStream.read(bytes);
        } else {
            byte[] bytes = new byte[SAMPLE_SIZE * 3];
            long numBytesBetweenSamples = (totalBytes - SAMPLE_SIZE * 3) / 2;

            // read first, middle and last bytes
            for (int n = 0; n < 3; n++) {
                digestInputStream.read(bytes, n * SAMPLE_SIZE, SAMPLE_SIZE);
                digestInputStream.skip(numBytesBetweenSamples);
            }
        }
        return new BigInteger(1, digest.digest()).toString(16);
    }
}

We've made a few assumptions here:

Two different files won't have the same fingerprints. It's not impossible that two files with different contents will have the same beginning, middle, and end bytes so they'll have the same fingerprints. Or they may even have different sample bytes but still hash to the same value (this is called a "hash collision"). To mitigate this, we could do a last-minute check whenever we find two "matching" files where we actually scan the full file contents to see if they're the same.

The most recently edited file is the duplicate. This seems reasonable, but it might be wrong—for example, there might be files which have been edited by daemons (programs that run in the background) after our friend finished duplicating them.

Two files with the same contents are the same file. This seems trivially true, but it could cause some problems. For example, we might have empty files in multiple places in our file system that aren't duplicates of each-other.

Given these potential issues, we definitely want a human to confirm before we delete any files. Still, it's much better than combing through our whole file system by hand!

Some ideas for further improvements:
If a file wasn't last edited around the time your friend got a hold of your computer, you know it probably wasn't created by your friend. Similarly, if a file wasn't accessed (sometimes your filesystem stores the last accessed time for a file as well) around that time, you know it wasn't copied by your friend. You can use these facts to skip some files.
Make the file size the fingerprint—it should be available cheaply as metadata on the file (so you don't need to walk through the whole file to see how long it is). You'll get lots of false positives, but that's fine if you treat this as a "preprocessing" step. Maybe you then take hash-based fingerprints only on the files which which have matching sizes. Then you fully compare file contents if they have the same hash.
Some file systems also keep track of when a file was created. If your filesystem supports this, you could use this as a potentially-stronger heuristic for telling which of two copies of a file is the dupe.
When you do compare full file contents to ensure two files are the same, no need to read the entire files into memory. Open both files and read them one block at a time. You can short-circuit as soon as you find two blocks that don't match, and you only ever need to store a couple blocks in memory.
Complexity
Each "fingerprint" takes O(1)O(1) time and space, so our total time and space costs are O(n)O(n) where nn is the number of files on the file system.

If we add the last-minute check to see if two files with the same fingerprints are actually the same files (which we probably should), then in the worst case all the files are the same and we have to read their full contents to confirm this, giving us a runtime that's order of the total size of our files on disc.

Bonus
If we wanted to get this code ready for a production system, we might want to make it a bit more modular. Try separating the file traversal code from the duplicate detection code.

What about concurrency? Can we go faster by splitting this procedure into multiple threads? Also, what if a background process edits a file while our script is running? Will this cause problems?

What about link files (files that point to other files or folders)? One gotcha here is that a link file can point back up the file tree. How do we keep our file traversal from going in circles?

What We Learned
The main insight was to save time and space by "fingerprinting" each file.

This question is a good example of a "messy" interview problem. Instead of one optimal solution, there's a big knot of optimizations and trade-offs. For example, our hashing-based approach wins us a faster runtime, but it can give us false positives.

For messy problems like this, focus on clearly explaining to your interviewer what the trade-offs are for each decision you make. The actual choices you make probably don't matter that much, as long as you show a strong ability to understand and compare your options.
