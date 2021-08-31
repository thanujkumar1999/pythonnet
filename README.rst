        public void UpdatePlot()
        {
            using (Py.GIL())
            {
                if (Local.HasKey("fig"))
                {
                    Python.PushLocal(Local);
                    Python.RunString(
                        "fig.set_size_inches(__figwidth__, __figheight__)" + Environment.NewLine +
                        "fig.tight_layout()" + Environment.NewLine +
                        "buf = io.BytesIO()" + Environment.NewLine +
                        "fig.savefig(buf, dpi=__dpi__, format='png')" + Environment.NewLine +
                        "buf_out = np.array(buf.getbuffer(), dtype = np.uint8)"
                    );
                    byte[] buf = Python.Get("buf_out");
                    Python.RunString(
                        "del buf_out\n" +
                        "del buf"
                    );
                    Local = Python.PopLocal();
                    Stream stream = new MemoryStream(buf);
                    Image image = Image.FromStream(stream);
                    PictureBox.Image = image;
                }
            }            
        }

