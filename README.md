# ezdma
Simple, zero-copy DMA to/from userspace.

## Usage

1. Specify which dmaengine-compatible DMA channels you'd like to create userspace-accessible device files for in your device tree:

    ```
    ezdma0 {
        compatible = "ezdma";
        dmas = <&loopback_dma 0 &loopback_dma 1>;
        dma-names = "loop_tx", "loop_rx";
        ezdma,dirs = <2 1>;     // direction of DMA channel: 
                                //   1 = RX (dev->cpu), 2 = TX (cpu->dev)
    };
    ```


2. After inserting the ezdma module, two devices, as named in your `dma-names` above, will become available:

    ```
        /dev/loop_tx
        /dev/loop_rx
    ```

3. Sending data is as simple as:

    ```
        int tx_fd = open("/dev/loop_tx", O_WRONLY);
        int rx_fd = open("/dev/loop_rx", O_RDONLY);

        write(tx_fd, tx_buf, xfer_size);    // send a DMA transaction
        read (rx_fd, rx_buf, xfer_size);
    ```

See [Documentation/devicetree/bindings/dma/ezdma.txt](../blob/master/Documentation/devicetree/bindings/dma/ezdma.txt) for additional example info.

## Compiling

A Makefile for out-of-tree building is supplied.  You just need to point it to the top-level directory of a kernel tree that you've already compiled.

    make KERNELDIR=/path/to/your/kernel

Currently the Makefile assumes you want to cross-compile for ARM by default, but you're free to override the `ARCH` and/or `CROSS_COMPILE` variables on the command line or on in your environment.  (I'd be interested to hear how it works on non-ARM platforms, as well!)

## Other info

Tested with:
- Xilinx AXI DMA on Zynq-7000 SoC.
    

Future enhancements:
* Allow a forced transaction size to be specified in sysfs (such that DMA transfers are always performed in increments of the given size).
  * This would be useful when sending AXI stream packets of a particular size and would allow simple usages like `cat packet_file > /dev/my_tx` or `cat /dev/my_rx > packet_file`.
* Add user-space scatter-gather `readv()`/`writev()` support.

In the future, I hope to refine and contribute ezdma to the mainline kernel.

## Resources

1. [Linux Device Tree Background]( http://devicetree.org/Device_Tree_Usage )
2. [Xilinx AXI DMA Driver]( https://github.com/Xilinx/linux-xlnx/blob/master/drivers/dma/xilinx/xilinx_axidma.c )

## License (GPL)

Copyright (C) 2015 Jeremy Trimble

This program is free software; you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation; either version 2 of the License, or
(at your option) any later version.

This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the
GNU General Public License for more details.

You should have received a copy of the GNU General Public License
along with this program; if not, see <http://www.gnu.org/licenses/>.


