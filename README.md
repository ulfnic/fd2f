# fd2f

```bash
fd2f COMMAND [PARAMETER...]
```
Executes COMMAND after converting file descriptors in PARAMETERs to temporary regular files.

## Details

If a PARAMETER has a value of '/dev/fd/'* or '/dev/self/fd/'* the file descriptor is written to temporary regular file and the PARAMETER is replaced with the path of that file. If PARAMETER ends in an extension, the extension is appended to the temporary file name.

After eligible PARAMETERs are replaced, COMMAND is executed with the new PARAMETERs.

Temporary files are written to a 0700 permissioned directory created within the temp directory defined by env variables: TMPDIR, then XDG_RUNTIME_DIR, then path /tmp is used in fallback order.

## Mock Process

```bash
Command: fd2f my-prog -f /dev/fd/43.png
  Write:             cat /dev/fd/43 > /tmp/fd2f__user_76.../2.png
Execute:      my-prog -f /tmp/fd2f__user_76.../2.png
 Remove:              rm /tmp/fd2f__user_76.../2.png
```

## Examples
```bash
# Docker Compose
fd2f sudo docker-compose -f <(
	cat <<-'EOF'
		My Docker Compose YAML
	EOF
).yml up
```
```bash
# NodeJS
printf '%s\n' 'pipe into nodejs' | fd2f node <(cat <<-'EOF'
	process.stdin.setEncoding('utf8');
	process.stdin.on('data', (data)=>{
		process.stdout.write(data)
	});
	process.stderr.write('Reading stdin...\n')
EOF
).js
```

## License

Licensed under AGPLv3. See LICENSE for details.



