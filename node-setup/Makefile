pdf_files = README.pdf \
			AWS.pdf \
			install.pdf \
			configure-files.pdf \
			cli.pdf \
			understanding-config-files.pdf \
			keys_and_addresses.pdf \
			faucet.pdf \
			transactions.pdf \
			register_key.pdf \
			node_keys.pdf \
			KES_period.pdf \
			core_relay.pdf \
			register_stakepool.pdf \
			start_your_nodes.pdf \
			retire_stakepool.pdf \
			logging.pdf \
			ekg.pdf \
			prometheus.pdf
png_files = cli.png

.PHONE: all
all: $(pdf_files) cli.pdf

$(pdf_files): %.pdf: %.md
	pandoc -f markdown -t latex -o $@ $<

$(png_files): %.png: %.dot
	dot -Tpng $< -o $@

cli.pdf: cli.png cli.md
	pandoc -f markdown -t latex -o cli.pdf cli.md
