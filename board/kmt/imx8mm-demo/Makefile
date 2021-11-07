# SPDX-License-Identifier: GPL-2.0+
#
# Copyright 2020 Toradex
#

obj-y += imx8mm-demo.o

ifdef CONFIG_SPL_BUILD
obj-y += spl.o
obj-$(CONFIG_IMX8M_LPDDR4) += lpddr4_timing.o
endif
