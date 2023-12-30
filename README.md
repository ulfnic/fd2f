# fd2f

```bash
fd2f EXECUTABLE [PARAMETER...]
fd2f sudo EXECUTABLE [PARAMETER...]
```

Executes a command after converting file descriptors in it's PARAMETERs into temporary regular files.

## Details

If a PARAMETER has a value of `/dev/fd/*` or `/dev/self/fd/*` it's written to a temporary file and the parameter is replaced with a path to that file.

If a file descriptor ends in an extension, its stripped from the path and appended to the temporary file.

Files a written to a 0700 permissioned directory specified by environment variables `TMPDIR` or `XDG_RUNTIME_DIR` in priority order, or `/tmp` as a fallback if both are empty.

## Example Process

```bash
     command: fd2f ocr-img -abc -f <(cat /dev/fd/0).png < take-screenshot
    executed: fd2f ocr-img -abc -f /dev/fd/43.png
 fd2f writes:                  cat /dev/fd/43 > "$temp_dir/2.png"
   fd2f runs:      ocr-img -abc -f "$temp_dir/2.png"
fd2f removes:                rm -r "$temp_dir"
```

## Examples of Use
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



